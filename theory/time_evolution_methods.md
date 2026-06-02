# 时间演化方法: 从 TDVP 到 Renormalizer 实现

**来源**: 论文 Ch.8 (TD-DMRG), 论文 Ch.7 (Frequency domain DMRG), DMRG textbook, Renormalizer 源码 `mps/mps.py`, `utils/configs.py`, `utils/rk.py`

## 1. 时间演化问题的框架

TD-DMRG 求解含时薛定谔方程:

```
i ∂_t |ψ(t)⟩ = Ĥ |ψ(t)⟩
```

精确形式解:
```
|ψ(t+dt)⟩ = exp(-i Ĥ dt) |ψ(t)⟩     (实时间)
|ψ(τ+dτ)⟩ = exp(-Ĥ dτ) |ψ(τ)⟩       (虚时间,基态搜索)
```

核心挑战:指数算符作用在 MPS 上导致键维度指数增长 \rightarrow 必须截断 \rightarrow 局部误差控制。

## 2. MPS/MPO 框架的统一视角

MPS 时间演化方法与 ML-MCTDH 有深层次的联系(论文 Ch.8 Sec.1.2):

- **ML-MCTDH**: 层状树张量网络,每层节点可有子节点和物理键
- **MPS**: ML-MCTDH 的一个极端特例——树退化为线性链,层数=格点数
- **从 TDVP 导出的 EOM 在两种框架下完全相同**(对于 MPS/线性 ML-MCTDH 结构)
- **MPS 的关键优势**: MPO 作用后自然保持 MPS 结构(键维度增大),而 ML-MCTDH 无法简单恢复树结构

这使得 MPS 可以自然地实现传播-压缩(P&C)方法和一般关联函数(如电流-电流关联函数),而 MCTDH 社区从未使用 MPO 概念。

## 3. 传播-压缩 (Propagation & Compress) 方法

### 3.1 Taylor 展开——prop_and_compress

直接 Taylor 展开时间演化算符:

```
exp(-iĤ dt) = Σ_{k=0}^{∞} (-i dt)^k/k! Ĥ^k
```

截断到 K 阶,然后求和并压缩:

```
terms = [Ĥ⁰|ψ⟩, (-i dt) Ĥ¹|ψ⟩, (-i dt)²/2 Ĥ²|ψ⟩, ...]
new_mps = compressed_sum(terms)
```

**实现**(源码 `mps.py:_evolve_prop_and_compress`):

1. 读取 Taylor 系数: `propagation_c = config.taylor_config.coeff`
   - 由 `TaylorExpansion(order)` 预计算: `coeff = [1.0 / k! for k in range(order+1)]`
   - `order` 默认: 非自适应=4, 自适应=5
2. 循环构建 termlist: `termlist[k] = mpo.contract(termlist[k-1])`
   - contract 过程中使用放松的压缩标准(允许键维度有限增长)
3. 自适应: 比较 K-1 阶和 K 阶 Taylor 截断的差异:

```
new_mps1 = compressed_sum(termlist[:K])        # K-1 阶
new_mps2 = compressed_sum(termlist[:K+1])       # K 阶
dis = new_mps1.distance(new_mps2)
p = (adaptive_rtol / (dis/(norm+1e-30)))^(1/order)
```

- 如果 p < 0.5: 拒绝该步,用更小的 dt 重试(guess_dt *= max(0.1, p))
- 否则: 接受,新的 guess_dt = dt \times min(p, 2.0)

这实质上是 **Taylor 截断误差估计**,而非 Trotter 半步比较。

### 3.2 Runge-Kutta 方法——prop_and_compress_tdrk

对于**含时哈密顿量** \hat{H}(t),使用普适龙格-库塔方法:

```
y' = f(t, y) = -i Ĥ(t) y
```

```python
def mpo_t(t, mps=None):
    """随时间变化的 MPO."""
    return Mpo(model, Op("sigma_z", 0, np.cos(t)))

new_mps = mps.evolve(mpo_t, dt)  # 自动调用 P&C TD-RK
```

**源码**(`_evolve_prop_and_compress_tdrk`):
- 使用 `EvolveConfig.rk_config` 的 Runge-Kutta 表格(a,b,c 系数)
- 每步中多个 k 项的加权求和(`compressed_sum` 带 batchsize=6 优化)
- 自适应: 比较 p 阶和 p-1 阶 RK 解的误差:

```
error = sum_i k_i × (b⁰_i - b¹_i) × dt     # 两套 b 系数的差
p = (adaptive_rtol / (error + 1e-30))^(1/order)
```

**tdrk4 vs tdrk**:
- `prop_and_compress_tdrk4`: 经典 RK4,固定步长(b 系数只一套)
- `prop_and_compress_tdrk`: 通用 RK + 自适应步长(如 RK5(4) 嵌入对)

### 3.3 P&C 方法的适用范围

**优势**: 简单,直观,可处理任意含时哈密顿量
**劣势**: Taylor 展开的截断误差随 dt 增大;每步需要多次 MPO 作用

**推荐用于**: 小系统(d\leq4, L\leq20),简单哈密顿量;含时哈密顿量

## 4. TDVP (Time-Dependent Variational Principle) 方法

### 4.1 基本原理

TDVP 在 MPS 的**切线空间**(tangent space)中投影 TDSE:

```
i ∂_t |ψ⟩ = P_{T_{|ψ⟩}} Ĥ |ψ⟩
```

其中 P_T 投影到当前 MPS 的切线空间——所有允许的 MPS 变化方向张成的子空间。

**关键**: 投影后的 EOM 自然保持了正则形式,可以在 sweep 中逐步积分。

### 4.2 投影分裂 (Projector Splitting)——tdvp_ps/tdvp_ps2

将切线空间投影符 P_T 分解为各格点投影符之和:
```
P_T = P_1 + P_2 + ... + P_L
```
使用 Trotter 分解对各格点 EOM 独立积分:

```
exp(-i P_T Ĥ dt) ≈ Π_{i=1}^L exp(-i P_i Ĥ dt)   (1-site)
                 ≈ ... exp(-i P_{i,i+1} Ĥ dt) ... (2-site)
```

**自适应步长**(`adaptive_tdvp` 装饰器): 两步 dt/2 传播与一步 dt 传播比较:
```
mps_half = evolve(evolve(mps, dt/2), dt/2)
mps_full = evolve(mps, dt)
dis = mps_full.distance(mps_half)
p = (0.75 × adaptive_rtol / (dis/(norm+1e-30)))^(1/3)    # O(dt³) 误差
```
p < 0.5 拒绝,否则接受并调整 guess_dt。此处的 0.75 和 1/3 来源于 Trotter 分解的 O(dt^{3}) 误差估计 (J. Chem. Phys. 146, 174107)。

### 4.3 变分均场 (VMF) 与恒均场 (CMF)——tdvp_vmf/tdvp_mu_cmf

**注意: Renormalizer 的 VMF/CMF 方法不是标准 MPS-TDVP(Haegeman et al., 2011),而是基于论文 Ch.8 描述的自洽均场理论**。两者都是 TDVP 的变体,但推导路径不同。

**VMF (Variable Mean Field)**: 在求解格点 \ell 的 EOM 时,环境矩阵(L 和 R)使用**实时更新的** MPS 张量计算——均场随 MPS 演化而变。EOM 是耦合的 ODE 系统,通过 scipy's `solve_ivp` (RK45) 积分。控制参数为 `ivp_rtol`, `ivp_atol`。

**CMF (Constant Mean Field)**: 环境矩阵使用**固定的** MPS(演化前的 MPS)计算。二阶 CMF 使用中点 MPS 做环境:
```
1. MPS_mid = evolve(MPS_0, dt/2)     (1st-order CMF)
2. MPS_t = evolve(MPS_0, dt; env=MPS_mid)  (2nd-order with midpoint env)
```

**矩阵展开正则化 (MU, Matrix Unfolding)**: 当 SVD 奇异值 s 很小时,1/s 导致数值爆炸。MU 通过正则化解决:
```
s_reg = s + ε · exp(-s/ε)    (ε = reg_epsilon, 默认 1e-10)
```
同时应用于逆矩阵 S^{-1} 的计算。源码: `mps/mps.py:_mu_regularize`。

**自动切换 (vmf_auto_switch=True)**:
- s_min > \sqrt(10\epsilon): \rightarrow 切换到 VMF (安全,不需要正则化)
- s_min < \epsilon: \rightarrow 切换到 MU-VMF (需要正则化)
- 逻辑位于 `_evolve_tdvp_mu_vmf` 末尾

### 4.4 各 TDVP 方法的比较

| 方法 | 机制 | 精度 | 速度 | 推荐 |
|------|------|------|------|------|
| `tdvp_vmf` | 变分均场 | 高 | 中 | 默认首选 |
| `tdvp_mu_vmf` | VMF + MU正则化 | 最高 | 较慢 | 奇异值小时 |
| `tdvp_mu_cmf` | CMF + MU | 较高 | 快 | 大系统 |
| `tdvp_ps` | 单格点 PS | 中 | 快 | 简单演化 |
| `tdvp_ps2` | 双格点 PS | 较高 | 慢 | 投影分裂中最鲁棒 |

## 5. 虚时间演化——基态搜索

虚时间演化是实时间演化的解析延拓 dt \rightarrow -i d\tau:

```
|ψ(τ+dτ)⟩ = exp(-Ĥ dτ) |ψ(τ)⟩
```

当 d\tau 足够小时,高能态衰减更快,最终投影到基态。

**注意**: exp(-\hat{H} d\tau) 非幺正,每次演化后 MPS 的范数会变化。需要归一化:
```python
mps.normalize("mps_and_coeff")  # 虚时间需要规范化系数
mps.normalize("mps_only")       # 实时间仅规范化MPS张量
```

**虚时间步长**: `guess_dt=1e-3/1j`(复数,虚部为正表示衰减)。通常比实时间步长小约 100 倍。

## 6. 键维度管理

时间演化中键维度自然增长。管理策略:

### 6.1 演化前扩展
```python
mps = mps.expand_bond_dimension(hint_mpo=mpo, coef=1e-10)
```
在现有零奇异值方向加入小量(coef),为 TDVP 提供增长空间。

### 6.2 演化中的压缩配置

对于 P&C 方法,contract 步骤使用**放松的**压缩标准(允许键维度增长),compressed_sum 步骤使用**严格的**压缩标准。通过动态切换 `compress_config` 实现。

```python
mps.compress_config = CompressConfig(
    criteria=CompressCriteria.both,
    threshold=1e-3, max_bonddim=128
)
```

### 6.3 磁盘卸载
```python
CompressConfig(dump_matrix_size=2**20, dump_matrix_dir="./tmp")
```

## 7. Renormalizer 中时间演化的完整配置

```python
from renormalizer.utils import EvolveConfig, EvolveMethod, CompressConfig, CompressCriteria

ham_mpo = Mpo(model, offset=Quantity(-E_gs))  # 能量偏移改善数值稳定性
mps = Mps.hartree_product_state(model, condition={0: [0, 1]})

mps.compress_config = CompressConfig(
    criteria=CompressCriteria.both,
    threshold=1e-3, max_bonddim=128,
)

mps.evolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_vmf,
    adaptive=True,
    guess_dt=0.05,
    adaptive_rtol=5e-4,
    reg_epsilon=1e-10,
    ivp_rtol=1e-5, ivp_atol=1e-8,
)

for istep in range(nsteps):
    mps = mps.evolve(ham_mpo, 0.05)
    t = (istep + 1) * 0.05
    sigma_z = mps.expectation(Mpo(model, Op("sigma_z", "spin")))
```

## 8. 两类方法的理论谱系

| 流派 | 代表方法 | 适应步长机制 | 优势 | 局限 |
|------|---------|------------|------|------|
| P&C | prop_and_compress | Taylor 阶数截断误差 | 可处理含时H | dt需小 |
| P&C | tdrk4/tdrk | RK 嵌入对误差 | 含时H+自适应 | 多次 MPO 作用 |
| TDVP PS | tdvp_ps/ps2 | Trotter 半步比较 | 快速 | 精度较低 |
| TDVP VMF | tdvp_vmf/mu_vmf | scipy RK45 积分器 | 最精确 | 耦合ODE求解慢 |
| TDVP CMF | tdvp_mu_cmf | scipy RK45 积分器 | 快速 | 近似环境 |

## 参考
- 论文 Ch.8: TD-DMRG (P&C, TDVP, Projector Splitting, finite-T)
- 论文 Ch.7: Frequency domain methods
- J. Chem. Phys. 146, 174107 (2017): Adaptive TDVP step-size control
- Renormalizer 源码: `mps/mps.py` (evolve 方法族), `utils/configs.py` (EvolveConfig), `utils/rk.py` (TaylorExpansion, RungeKutta)
