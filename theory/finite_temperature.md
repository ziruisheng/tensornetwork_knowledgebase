# 有限温度方法: 热态构造与密度矩阵

**来源**: 论文 Ch.8 Sec.3 (Finite temperature methods), Renormalizer 源码 `mps/thermalprop.py`, `mps/mpdm.py`

## 1. 为什么需要有限温度方法?

对于含声子的化学系统,核自由度在室温(~300K \approx 200 cm^{-}^{1})下热激发显著:
- 振动模式的玻色-爱因斯坦占据: \langlen_i\rangle = [exp(\beta\omega_i) - 1]^{-}^{1}
- 典型 C-C 伸缩模式 \omega~1500 cm^{-}^{1} \rightarrow \langlen\rangle \approx 0.001(无热激发)
- 低频模式 \omega~50 cm^{-}^{1} \rightarrow \langlen\rangle \approx 3.0(显著热激发)
- 结论: 低频模式的热效应不可忽略

有限温度方法的核心任务: **构造正则系综热密度矩阵 \rho(T)**:
```
ρ(T) = exp(-βĤ) / Z    (Z = Tr[exp(-βĤ)], β = 1/k_B T)
```

然后在 \rho(T) 上计算含时关联函数或谱函数。

## 2. 纯化 (Purification) 方案

### 2.1 形式理论

纯化将混合态密度矩阵映射到扩展 Hilbert 空间的一个**纯态**:

```
ρ = Tr_{ancilla}(|Ψ⟩⟨Ψ|)
```

其中 |\Psi\rangle 生活在 system \otimes ancilla 的直积空间,ancilla 是系统的副本。

**无限温度纯态**: T=\infty 时,\rho(\infty) = I/d^L,对应的纯态为各格点最大纠缠:

```
|Ψ(∞)⟩ = 1/√d^L ⊗_{i=1}^{L} (Σ_{σ_i} |σ_i⟩_sys ⊗ |σ_i⟩_anc)
```

### 2.2 虚时间演化——从 \beta=0 到目标温度

从 |\Psi(\infty)\rangle 出发,仅在系统自由度上作用虚时间演化解:

```
|Ψ(β/2)⟩ = exp(-Ĥ⊗I_anc · β/2) |Ψ(∞)⟩
```

因子 1/2 的出现是因为: **纯化要求 \rho = Tr_ancilla(|\Psi\rangle\langle\Psi|)**。代入:
```
Tr_ancilla(|Ψ(β/2)⟩⟨Ψ(β/2)|) = exp(-βĤ/2) · I · exp(-βĤ/2) = exp(-βĤ)
```
两个 exp(-\betaH/2) — 左乘 bra、右乘 ket — 共同给出 exp(-\betaH)。这源自纯化的基本定义,不是"为了方便时间演化分裂"的工程技巧。

**为什么演化到 \beta/2 而非 \beta?** 后续的含时演化确实受益于这一结构:
```
C(t) = Tr{ρ(T) Â(t) Â(0)} = Tr{exp(-βĤ/2) exp(iĤt) Â exp(-iĤt) Â exp(-βĤ/2)}
      = ⟨Ψ^L(β/2,t)| Â |Ψ^R(β/2,t)⟩
```
将 \rho^{1/2} 分配给 bra 和 ket 各一份,可分别演化。

### 2.3 在 MPS 中的表示——MpDm

纯化态的系统-辅助系统 pair 在 MPS 中通过**物理维度加倍**表示:

每个格点的物理维度从 d 变为 d^{2}(sys \otimes anc),张量形状为 (d, d, D_left, D_right)。

**MpDm** (Matrix Product Density Operator) 是这一表示的具体实现:

```python
from renormalizer.mps import MpDm

# 激发态空间,无限温度 (电子+声子均 T=∞)
mpdm = MpDm.max_entangled_ex(model)
# 内部: Mps.ground_state(max_entangled=True) 后作用 Σ a†_i (升到1激子)

# 基态空间,电子在 GS (仅声子 T=∞)
mpdm = MpDm.max_entangled_gs(model)
# 内部: Mps.ground_state(max_entangled=True) → MpDm

# 从|ψ⟩⟨ψ|构造
mpdm = MpDm.from_mps(mps)
```

**MpDm 与 MPS 的关键区别**:
- 每个格点的张量形状为 (D_left, d, d, D_right)——4 个维度而非 MPS 的 3 个
- `evolve` 方法同时作为 MPS(时间演化)和 MPO(密度矩阵的算符作用)
- 期望值: `mpdm.expectation(mpo)` = Tr(\rho \hat{A})
- 支持 canonicalise, dump, load

## 3. 虚时间热传播——ThermalProp

### 3.1 算法

```python
from renormalizer.mps import ThermalProp, MpDm
from renormalizer.utils import EvolveConfig, EvolveMethod

# 构造初始 MpDm (激发态空间,T=∞)
mpdm = MpDm.max_entangled_ex(model)

thermal = ThermalProp(
    init_mpdm=mpdm,
    h_mpo_model=model,
    exact=False,
    # exact=True: 仅 Holstein 模型的实空间 GS/EX 情况,利用各格点独立对角化
    #   快速但不通用;此时各格点独立对角化,无需截断
    evolve_config=EvolveConfig(
        method=EvolveMethod.tdvp_mu_cmf,
        adaptive=True,
        guess_dt=-1j * temperature.to_beta() / 1000,  # ~β/1000
        adaptive_rtol=5e-4,
    ),
)

# 演化到 β/2: dt ~ β/1000, nsteps ~ 500-1000 取决于目标精度
thermal.evolve(evolve_dt=-1j * temperature.to_beta()/1000, nsteps=500)
rho_half = thermal.latest_mps  # MpDm at β/2
```

### 3.2 虚时间步长与 \beta 标度

一般的步长选择:
```
guess_dt ≈ β / 1000 ≈ 1/(k_B T × 1000)
```

| 物理温度 T | \beta (a.u.) | \beta/1000 (a.u.) | 虚时间步长 dt |
|-----------|----------|---------------|-------------|
| 300K | ~1053 | ~1.05 | ~1.0/1j |
| 200K | ~1580 | ~1.58 | ~1.5/1j |
| 100K | ~3160 | ~3.16 | ~3.0/1j |

**注意**: nsteps \approx \beta/(2\timesdt) = 500。不同目标温度用相同的 ~500 步,仅步长变化。

### 3.3 精确传播 vs 一般传播

**精确传播**(`exact=True`):
```
仅适用于 Holstein 模型。Mpo.exact_propagator 在各格点上独立对角化局域
哈密顿量,一步构造 exp(-xĤ) 的精确 MPO,无需 TDVP 积分和截断。
```
此模式下,演化极快,不积累 TDVP 误差,但限于 Holstein GS/EX 空间。

**一般传播** (`exact=False`, 默认):
使用 TDVP CMF/VMF,适用于任意 Hamiltonian,需要管理键维度增长和截断。

### 3.4 热力学性质

`ThermalProp` 在每步演化后自动记录:
```python
thermal.energies              # 能量 vs 虚时间步
thermal.e_occupations_array   # 电子占据数
thermal.ph_occupations_array  # 声子占据数
thermal.vn_entropy_array      # von Neumann 纠缠熵
```

**纠缠熵行为**: 随温度降低,基态纠缠 > 激发态纠缠。低温下需要更大的 bond dimension。

**自定义观测**:
```python
from renormalizer.property import Property

class MyProperties(Property):
    def calc_properties(self, mpdm_state):
        val = mpdm_state.expectation(Mpo(self.model, op))
        self.prop_res.setdefault("my_obs", []).append(val)

thermal = ThermalProp(init_mpdm=mpdm, properties=MyProperties(model))
```

## 4. 双段冷却策略(低温加速)

对于较低温度(T < 150K),单阶段冷却可能需要过多步数:

```python
from renormalizer.mps import load_thermal_state

# 阶段1: 粗冷却 (大步长,快速逼近)
thermal_coarse = ThermalProp(init_mpdm=mpdm,
    evolve_config=EvolveConfig(guess_dt=-1j * 3.0, ...))
thermal_coarse.evolve(evolve_dt=-1j * 3.0, nsteps=300)
thermal_coarse.latest_mps.dump("thermal_coarse.npz")

# 阶段2: 精冷却 (加载粗态,小步长精化)
rho_loaded = load_thermal_state(model, "thermal_coarse.npz")
thermal_fine = ThermalProp(init_mpdm=rho_loaded,
    evolve_config=EvolveConfig(guess_dt=-1j * 1.0, ...))
thermal_fine.evolve(evolve_dt=-1j * 1.0, nsteps=300)
```

## 5. 从热态到可观测量

构造出 \rho^{1/2}(T) 后,有两种主要应用路径:

### 5.1 Kubo 输运——关联函数

```
C(t) = Tr{exp(-βĤ/2) exp(iĤt) Â exp(-iĤt) Â exp(-βĤ/2)} / Z
     = ⟨Ψ^L(t) | Â | Ψ^R(t)⟩ / Z
其中: |Ψ^L(t)⟩ = exp(-iĤt) exp(-βĤ/2)|Ψ_0⟩     (R-channel, 向前)
      |Ψ^R(t)⟩ = exp(-iĤt) Â exp(-βĤ/2)|Ψ_0⟩   (L-channel, 向前 + 算符)
```
**注意**: 在实际实现中,两个 channel 都向前演化(用相同的演化步)。bra 的 `exp(i\hat{H}t)` 通过取 ket 演化的共轭等价实现。

### 5.2 热平均

```
⟨Â⟩_T = Tr(ρ(T) Â) / Z = ⟨Ψ(β/2) | Â | Ψ(β/2)⟩ / Z
```
直接用 MpDm 的 `expectation` 方法计算。

## 6. Renormalizer 中的完整示例

```python
from renormalizer.mps import ThermalProp, MpDm, load_thermal_state
from renormalizer.utils import EvolveConfig, EvolveMethod, Quantity, CompressConfig

# 初始化
mpdm = MpDm.max_entangled_ex(model)
beta = Quantity(300, "K").to_beta()   # 1053 a.u.
guess_dt = -1j * beta / 1000          # ~1.05/1j

# 配置
ievolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_mu_cmf,
    adaptive=True, guess_dt=guess_dt,
)
compress_config = CompressConfig(threshold=1e-4)

# 传播
thermal = ThermalProp(
    init_mpdm=mpdm,
    h_mpo_model=model,
    evolve_config=ievolve_config,
)
thermal.evolve(evolve_dt=guess_dt, nsteps=500)

# 保存和复用
thermal.latest_mps.dump("thermal_beta_half.npz")
rho_loaded = load_thermal_state(model, "thermal_beta_half.npz")
```

## 参考
- 论文 Ch.8 Sec.3: Finite temperature methods (imaginary time, thermofield)
- Feiguin & White, PRB 72, 220401 (2005): Finite-T DMRG via imaginary time
- Verstraete, Garcia-Ripoll, Cirac. PRL 93, 207204 (2004): MPS purification
- Renormalizer 源码: `mps/thermalprop.py`, `mps/mpdm.py`
