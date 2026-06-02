# MPS 基本原理与 Renormalizer 实现

**来源**: DMRG textbook (Schollwoeck, 2011) Ch.1-2, 论文 Ch.3, Renormalizer 源码

## 1. 多体量子态的张量积表示

### 1.1 从多体希尔伯特空间到张量网络

对于一个由 L 个格点组成的量子系统,每个格点具有 d 维局域希尔伯特空间,全系统的希尔伯特空间为 L 个 d 维空间的张量积:

```
ℋ = ℋ₁ ⊗ ℋ₂ ⊗ ... ⊗ ℋ_L    dim(ℋ) = d^L
```

任一纯态可展开为:

```
|ψ⟩ = Σ_{σ₁...σ_L} c_{σ₁...σ_L} |σ₁⟩ ⊗ |σ₂⟩ ⊗ ... ⊗ |σ_L⟩
```

其中 c 是一个 d^L 维张量,指数增长——这就是"维度灾难"。

**Renormalizer 对应**: `BasisSet.nbas` = d, `Model.pbond_list` = 所有格点的物理维度列表。

```python
from renormalizer import BasisHalfSpin, BasisSHO, Model

# 每个 DoF 必须有唯一名称,否则 Model.__init__ 抛出 ValueError
b_spin1 = BasisHalfSpin(0)                     # d=2: |↑⟩, |↓⟩
b_spin2 = BasisHalfSpin(1)                     # d=2
b_ph1 = BasisSHO("v_0", omega=1500, nbas=8)   # d=8: phonon Fock |0⟩..|7⟩
b_ph2 = BasisSHO("v_1", omega=800, nbas=6)    # d=6
model = Model([b_spin1, b_ph1, b_spin2, b_ph2], ham_terms)
# model.nsite=4, model.pbond_list=[2,8,2,6], dim(ℋ)=2×8×2×6=192
```

### 1.2 面积律——MPS有效性的物理根源

对于一维有能隙(gapped)系统,基态纠缠熵满足面积律:

```
S(ρ_A) ~ const    (与系统尺寸无关,只取决于子系统边界面积)
```

**重要提醒**: 对于临界(无能隙)一维系统,纠缠熵随系统尺寸对数增长 S ~ c/3\cdotlog(L),MPS 所需的键维度 D 随之多项式增长。实际计算中通过增大 D 来处理。Renormalizer 适用于两者。

**长程相互作用**(如量子化学 Coulomb 势):对于(准)一维体系(共轭聚合物),Shuai 等人证明高精度仍成立(见论文 Ch.3)。二维体系映射为一维后,长程跳跃导致纠缠增大,需更大 bond dimension。

## 2. Schmidt 分解与密度矩阵截断

### 2.1 Schmidt 分解

将系统分为 A 和 B 两部分,任意纯态可唯一分解为:

```
|ψ⟩ = Σ_{α=1}^{min(dimA,dimB)} s_α |α⟩_A ⊗ |α⟩_B
```

s_\alpha \geq 0 为 Schmidt 系数,|\alpha\rangle_A, |\alpha\rangle_B 为正交归一基。

- 约化密度矩阵: \rho_A = \Sigma_\alpha s_\alpha^{2} |\alpha\rangle\langle\alpha|_A
- 纠缠熵: S = -\Sigma_\alpha s_\alpha^{2} log_{2} s_\alpha^{2}
- 截断: 保留最大的 D 个 s_\alpha,丢弃权重 = 1 - \Sigma_{\alpha=1}^D s_\alpha^{2}

### 2.2 Renormalizer 中的截断实现

```python
from renormalizer.utils import CompressConfig, CompressCriteria

# 按阈值: 丢弃奇异值 < threshold
CompressConfig(criteria=CompressCriteria.threshold, threshold=1e-3)

# 按固定键维度
CompressConfig(criteria=CompressCriteria.fixed, max_bonddim=64)

# 同时使用两者(取更严格者)
CompressConfig(criteria=CompressCriteria.both, threshold=1e-3, max_bonddim=64)
```

源码 `utils/configs.py:compute_m_trunc`: 三种截断判定方式 (threshold/fixed/both),取 SVD 奇异值后保留满足条件的奇异值个数。

## 3. 矩阵乘积态(MPS)——规范形式与环境网络

### 3.1 MPS 定义

将 c_{\sigma_{1}...\sigma_L} 分解为 L 个三阶张量:

```
|ψ⟩ = Σ_{σ₁...σ_L} Σ_{a₁...a_{L-1}} A^{σ₁}_{1,a₁} A^{σ₂}_{a₁,a₂} ... A^{σ_L}_{a_{L-1},1} |σ₁...σ_L⟩
```

图形:
```
[A¹]--[A²]--[A³]--...--[A^L]
  |     |     |           |
  σ₁    σ₂    σ₃         σ_L
```

- 横向指标 a_i: **键指标**(bond index),维度 D
- 纵向指标 \sigma_i: **物理指标**,维度 d
- 参数量: O(L\cdotd\cdotD^{2}) vs 指数 O(d^L)

### 3.2 规范自由度与正则形式

MPS 表示不唯一。在键 j 插入 XX^{-}^{1},分别吸收到 M^{\sigma_j} 和 M^{\sigma_{j+1}} 中,得到等价表示。利用这一自由度加规范条件:

| 形式 | 条件 | 用途 |
|------|------|------|
| 左正则 | \Sigma(A^{\sigma})^\daggerA^{\sigma} = I | 右扫,构建左环境 |
| 右正则 | \Sigma B^{\sigma}(B^{\sigma})^\dagger = I | 左扫,构建右环境 |
| 混合正则 | A...A M B...B | sweep 中的正交中心 |

**混合正则的关键优势**:
- 重叠简化为 \langle\psi|\psi\rangle = tr(M^^\daggerM),不需要求逆
- 本征问题 Hv=\lambdav 是标准形式而非广义 Hv=\lambdaNv

**Renormalizer 源码**: `mps/mps.py` 中 `to_right` 属性标记规范方向,`ensure_left_canonical()`/`ensure_right_canonical()` 做 QR/SVD 规范化。

### 3.3 MPS 的三种构造方式

```python
# 1. Hartree 直积态 (D=1, 初态每个格点独立设局域态)
# 注意: DoF 名称必须匹配 model 中定义的名称
mps = Mps.hartree_product_state(model, condition={1: [0,1], "v_0": 2})

# 2. 随机态 (D=m_max, 保持量子数守恒, DMRG sweep 起点)
mps = Mps.random(model, qntot=[5,5], m_max=100, percent=1.0)

# 3. T=0/T=∞ 基态
mps = Mps.ground_state(model, max_entangled=False)
```

`random` 的源码逻辑: 逐格点构造随机张量 \rightarrow 在键处随机本征值分解 \rightarrow `select_basis` 截断到 m_max 并保留量子数守恒。

### 3.4 MPS 加法与压缩

两个 MPS 相加 = 键维度直和(压缩前键维度和等于两态键维度之和),然后必须压缩:

```
|ψ⟩ = |φ⟩ + |χ⟩  →  D_ψ[i] = D_φ[i] + D_χ[i]
|ψ⟩ → SVD 截断 → D 回到可控范围
```

`mps/lib.py: compressed_sum(mps_list, batchsize=5)` 对 MPS 列表执行无权和求和+压缩。加权求和需上游调用 `mps.scale(weight)` 后再传入 `compressed_sum`。例如 Runge-Kutta 时间演化: `compressed_sum([y, k1.scale(a1*dt), k2.scale(a2*dt), ...])`。

## 4. 期望值: 左/右环境网络

### 4.1 环境张量

期望值 \langle\psi|\hat{O}|\psi\rangle 可分解为:

```
⟨ψ|Ô|ψ⟩ = Σ_{all indices} L^{[n-1]}_{a,a',b} · W^{σ_n,σ'_n}_{b,b'} · R^{[n+1]}_{c,c',b'} · A^{σ_n}_{a,c} · (A^{σ'_n})_{a',c'}*
```

其中 a,a' 为左侧键指标,c,c' 为右侧键指标,b,b' 为 MPO 键指标。所有指标通过张量收缩隐式求和。

L^{[n]} = 格点 1 到 n 的完整左收缩,R^{[n]} = 格点 n+1 到 L 的右收缩。

**递推关系**(右扫):
```
L^{[n]} = L^{[n-1]} · A^{σ_n} · W^{σ_n,σ'_n} · (A^{σ'_n})^*
```

在 DMRG sweep 中,只需增量更新——已计算的环境复用,这是 DMRG O(L\cdotD^{3}) 而非 O(L^{2}\cdotD^{3}) 的关键。

### 4.2 Renormalizer 实现

`mps/lib.py:Environ` 类:
```python
from renormalizer.mps.lib import Environ
environ = Environ(mps, mpo, "L")   # 从左构建
L_n = environ.read("L", n)
environ = Environ(mps, mpo, "R")   # 从右构建
R_n = environ.read("R", n)
```

`mps/mps.py:_expectation_path` 定义 optimal einsum contraction path,`expectations` 通过缓存中间环境加速批量计算。

## 5. MPO——矩阵乘积算符

### 5.1 定义

```
Ô = Σ_{σ₁σ'₁...} Σ_{b₁...b_{L-1}} W^{σ₁σ'₁}_{1,b₁} ... W^{σ_Lσ'_L}_{b_{L-1},1} |σ₁...σ_L⟩⟨σ'₁...σ'_L|
```

### 5.2 Renormalizer 的自动MPO构造

无需手动设计 W 矩阵。用户提供 Op/OpSum 列表,Renormalizer 用图论算法自动构建最优 MPO:

```
Op("a† a", [0,1], t) + Op("a† a", [1,0], t)
  ↓ construct_symbolic_mpo: 有向图+匈牙利匹配→最优符号MPO
  ↓ symbolic_mo_to_numeric_mo: 符号→数值矩阵元
  ↓ Mpo(model, ham_terms)
```

算法细节见 Ren et al., JCP 153, 084118 (2020)。自动保证 MPO bond dimension 最优。

### 5.3 MPO 应用

```python
new_mps = mpo.contract(mps)          # Ô|ψ⟩, 键维度 D_mps × D_mpo
new_mps = mpo.apply(mps)             # contract, 不自动压缩
# apply(canonicalise=True) 会触发规范化,但主要压缩由上层 sweep 的截断控制
```

## 6. 量子数守恒——Block-Sparse MPS

### 6.1 原理

若哈密顿量具有对称性(N守恒、S_z守恒),约化密度矩阵在对称子空间之间分块对角。Block-sparse 表示:
- 张量参数大幅减少
- 自然限制在正确对称子空间
- 通过 `qntot` 直接 target 特定粒子数/自旋的态

### 6.2 Renormalizer 实现

每个 `BasisSet` 定义 `sigmaqn` — 局域基矢的量子数数组,形状 (nbas, qn_size):

```python
# BasisHalfSpin: 默认 sigmaqn=[[0],[0]] (自旋向上和向下均携带量子数 0)
#   用户需显式传入 sigmaqn=[[0],[1]] 或 [[0,0],[1,0]] 来追踪 S_z
# BasisSimpleElectron: sigmaqn=[[0],[1]] (默认: 0个电子→0, 1个电子→+1)
# BasisSHO: sigmaqn=[[0],[0],...] (声子态不改变量子数)
```

量子数传播:
- `mps.qn[i]`: 键 i 左侧基的量子数集合
- `mps.qntot`: 全局总量子数
- `add_outer(qn[i], sigmaqn[i])` \rightarrow 键 i+1 的所有量子数组合
- `get_qn_mask` \rightarrow 滤除不匹配的组合(检查精确相等: `np.all(qnmat == qntot, axis=-1)`)

Block-sparse SVD (`svd_qn.svd_qn`): 每个 (qn_left, qn_right) 块独立 SVD,截断按块分配。

### 6.3 "单格点"vs"双格点"——局部极小问题

**单格点陷阱**(教科书 2.5.3): 在 MPS sweep 中,从未合并格点的单格点 SVD 截断时,键指标的量子数分布保持冻结——同一量子数块内的基向量的个数不变。若初始分布与最优基态不匹配,算法困在局部极小。这一陷阱在有量子数守恒和无量子数守恒的系统中均存在——根本原因是单格点算法不能扩充键基空间。

**双格点方案**: 同时优化两个相邻格点 \rightarrow SVD 在更大空间进行 \rightarrow 允许量子数自然重分布 \rightarrow 更鲁棒,O(d) 倍代价。

**Renormalizer**: `optimize_mps` 用 `method="2site"`;`OptimizeConfig.procedure` 中的 `percent` 参数控制 `select_basis` 在每个量子数块平均分配的噪声基矢比例。

## 7. DMRG 基态优化——逐格点变分方法

### 7.1 变分原理

混合正则形式(正交中心在 \ell):
```
|ψ⟩ = A^{1}...A^{ℓ-1} M^{ℓ} B^{ℓ+1}...B^{L}
```
仅 M^{\ell} 可变。变分 \langle\psi|\hat{H}|\psi\rangle - \lambda\langle\psi|\psi\rangle 给出:

```
H_eff · v = λv
```

其中 H_eff 维度为 (D_left \times d_\ell \times D_right)^{2},由 L^{[\ell]}, W, R^{[\ell+1]} 收缩得到。

**关键简化**: 正则性保证 N=I,问题为标准本征值问题(非广义),可使用 Davidson/Lanczos。

### 7.2 Sweep 算法

```
右扫 ℓ=1→L-1:
  1. Davidson 求解 H_eff v=λv
  2. v→M^{σ_ℓ}, SVD 左正则化为 A^{σ_ℓ}
  3. SVD 剩余 → 右侧 M^{σ_{ℓ+1}}(新初始猜测)
  4. L^{[ℓ]} ← L^{[ℓ-1]}·A·W·A†
  5. ℓ→ℓ+1

左扫 ℓ=L→2:
  1-2. 同上,右正则化为 B^{σ_ℓ}
  3. SVD 剩余 → 左侧
  4. R^{[ℓ]} ← W·B·B†·R^{[ℓ+1]}
  5. ℓ→ℓ-1
```

### 7.3 Renormalizer 实现

`mps/gs.py:optimize_mps`:
```python
energies, mps = optimize_mps(mps, mpo)
```
- `OptimizeConfig.procedure` = [[bond_dim, noise_percent], ...] 逐轮递增
  - 初始高噪声(0.3-0.5)探索希尔伯特空间,后期噪声\rightarrow0 精化
  - 噪声比例控制 `select_basis` 在每个量子数块内平均分配的基矢比例
- state-averaged: `OptimizeConfig.nroots=N`,配合 `StackedMpo`
- BlockEnv 加速: `OptimizeConfig.use_block_env=True`,阈值 `block_env_threshold=1e-14`
- 收敛标准: `e_rtol=1e-6`, `e_atol=1e-8`

## 8. MPS 生命周期

```
Mps.random/hartree_product_state/ground_state → optimize_mps → mps.dump → Mps.load
                    ↓                              (DMRG GS)       ↓
                mps.evolve(mpo, evolve_dt) ← ← ← ← ← ← ← ← ← ← ← ← ← ← ←
                    ↓
                mps.expectation(mpo), mps.ph_occupations, mps.e_occupations
```

## 参考
- Schollwoeck, U. Ann. Phys. 326, 96-192 (2011).
- Ren, Li, Jiang, Shuai. J. Chem. Phys. 153, 084118 (2020).
- Renormalizer 源码: `renormalizer/mps/`
- 论文 Ch.3: DMRG for semi-empirical quantum chemistry (对称化 DMRG)
