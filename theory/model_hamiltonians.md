# 模型哈密顿量:从物理原理到 Renormalizer 实现

**来源**: 论文 Ch.3 (DMRG for semi-empirical quantum chemistry), 论文 Ch.8 (TD-DMRG), DMRG textbook Ch.1, Renormalizer 源码

## 1. 第二量子化形式——通用框架

Renormalizer 使用第二量子化语言描述多体系统。对于包含 L 个格点的体系:

```
|ψ⟩ = Σ_{n₁_↑...n_L_↓} c_{n₁_↑...n_L_↓} |n₁_↑ n₁_↓...n_L_↓⟩
```

其中 n_{i\sigma} \in {0,1} 标记格点 i 上自旋 \sigma 的电子占据数。产生/消灭算符通过 Jordan-Wigner 变换处理费米子符号:

```
^c†_{iσ} |..., n_{iσ}, ...⟩ = (-1)^{Σ_{j<i,τ} n_{jτ}} |..., n_{iσ}+1, ...⟩
^c_{iσ} |..., n_{iσ}, ...⟩  = (-1)^{Σ_{j<i,τ} n_{jτ}} |..., n_{iσ}-1, ...⟩
```

一般哈密顿量写为:
```
Ĥ = Σ_{ij,σ} T_{ij} ^c†_{iσ} ^c_{jσ} + ½ Σ_{ijkl,σσ'} V_{ijkl} ^c†_{iσ} ^c†_{jσ'} ^c_{lσ'} ^c_{kσ}
```

在 Renormalizer 中,这通过 `Op` 符号算符结合 `BasisSet` 的矩阵元自动转换为 MPO。

**重要: Renormalizer 的 \sigma 算符约定**: `BasisHalfSpin` 中 `"+"` 对应 \sigma^{+} (|1\rangle\rightarrow|0\rangle, 消灭电子, \DeltaN = -1),`"-"` 对应 \sigma^{-} (|0\rangle\rightarrow|1\rangle, 产生电子, \DeltaN = +1)。这与某些文献中 \sigma^{+} 表示产生的标准自旋约定相反。下文所有 JW 字符串和量子数均遵循 Renormalizer 约定。

## 2. Hubbard 模型

### 2.1 物理原理

Hubbard 模型是描述关联电子系统的最小模型:

```
Ĥ_Hubbard = -t Σ_{⟨i,j⟩,σ} (^c†_{iσ} ^c_{jσ} + h.c.) + U Σ_i ^n_{i↑} ^n_{i↓}
```

- **第一项(跃迁项)**: 电子在最近邻格点间跃迁,动能带宽 W = 4t (1D)
- **第二项(在位库仑排斥)**: 两个电子占据同一格点的能量代价

**两个极限**:
- **U\llt (弱关联极限)**: 单电子图像成立,电子形成能带,金属行为
- **U\ggt (强关联极限)**: 电子局域化,半填充时系统变为 Mott 绝缘体;有效哈密顿量退化为 Heisenberg 模型 J = 4t^{2}/U

Hubbard 模型在一维有 Bethe ansatz 精确解(Lieb & Wu, 1968),二维无解析解。

**物理上**: 每个空间格点的局域基有 d=4 个态 (|0\rangle, |\uparrow\rangle, |\downarrow\rangle, |\uparrow\downarrow\rangle)。**Renormalizer 实现上**: 将每个空间格点拆分为两个 `BasisHalfSpin` 格点(up 和 down),每个 d=2,通过 Jordan-Wigner 弦连接。

### 2.2 在 Renormalizer 中的实现

Hubbard 模型通过自定义 `BasisHalfSpin` 和 `Op` 构建,需要处理 Jordan-Wigner 弦:

```python
from renormalizer import Model, Mps, Mpo, BasisHalfSpin, Op, optimize_mps
import numpy as np

nsites = 10
t = -1; U = 4

# 格点排布: 0up, 0down, 1up, 1down, ...
# 量子数: [N_alpha, N_beta]
# Renormalizer 约定: "+" = σ⁺ (消灭, ΔN=-1), "-" = σ⁻ (产生, ΔN=+1)
qn_up  = {"+": [-1,0], "-": [1,0], "Z": [0,0]}
qn_down = {"+": [0,-1], "-": [0,1], "Z": [0,0]}

# 跃迁项——通过 Jordan-Wigner 弦连接
ham_terms = []
for i in range(2*(nsites-1)):
    if i % 2 == 0:  # up hop + down Z string
        qn = [qn_up["Z"], qn_up["+"], qn_down["Z"], qn_up["-"]]
    else:            # down hop + up Z string
        qn = [qn_down["Z"], qn_down["+"], qn_up["Z"], qn_down["-"]]

    op1 = Op("Z + Z -", [i,i,i+1,i+2], factor=t, qn=qn)
    op2 = Op("Z - Z +", [i,i,i+1,i+2], factor=-t, qn=qn)  # h.c.
    ham_terms.extend([op1, op2])

# 在位库仑项 U n_{i↑} n_{i↓}
# "^c†_{i↑} ^c_{i↑} ^c†_{i↓} ^c_{i↓}" → JW: σ⁻_i σ⁺_i σ⁻_{i+1} σ⁺_{i+1}
for i in range(0, 2*nsites, 2):
    op = Op("- + - +", [i,i,i+1,i+1], factor=U,
            qn=[[1,0], [-1,0], [0,1], [0,-1]])
    ham_terms.append(op)

# 自定义 sigmaqn: up/down 格点有不同的量子数结构
basis = []
for i in range(2*nsites):
    if i % 2 == 0:
        sigmaqn = np.array([[0,0], [1,0]])   # up: (0,0)空 (1,0)1个↑电子
    else:
        sigmaqn = np.array([[0,0], [0,1]])   # down: (0,0)空 (0,1)1个↓电子
    basis.append(BasisHalfSpin(i, sigmaqn=sigmaqn))

model = Model(basis, ham_terms)
mpo = Mpo(model)
mps = Mps.random(model, qntot=[5,5], m_max=100, percent=1.0)
energies, mps = optimize_mps(mps, mpo)
```

### 2.3 Jordan-Wigner 变换的核心模式

Hubbard 模型中跃迁项 `^c^\dagger_{i\sigma} ^c_{j\sigma}` 的 JW 表示为:
```
^c†_i ^c_j  =  σ⁻_i · Π_{k=i+1}^{j-1} σ^z_k · σ⁺_j
```
其中 `\sigma^{-}` 产生电子(Renormalizer 约定),`\sigma^{+}` 消灭电子,`\sigma^z` 提供正确的费米子符号。

在 `Op` 语法中,四个算符的乘积 `Z \cdot + \cdot Z \cdot -` 依次对应(Renormalizer 约定下):
- 第一个 `Z`: 在跃迁起点之前的所有格点的 \sigma^z 弦
- `+` (\sigma^{+}): 在跃迁起点的消灭算符
- 第二个 `Z`: 跃迁起终点之间的 \sigma^z 弦
- `-` (\sigma^{-}): 在跃迁终点的产生算符

量子数数组的对应: `qn=[[0,0], [-1,0], [0,0], [1,0]]` 依次表示 [\DeltaN_\alpha, \DeltaN_\beta]。

**注意**: 哈共轭项 `^c^\dagger_j ^c_i` 对应 `Z \cdot - \cdot Z \cdot +`,量子数 `[..., [1,0], ..., [-1,0]]`。两项一起保证哈密顿量厄米。

## 3. 扩展 Hubbard 与 PPP 模型

### 3.1 扩展 Hubbard 模型

```
Ĥ = -t Σ (^c†_i ^c_j + h.c.) + U Σ ^n_{i↑} ^n_{i↓} + V Σ_{⟨i,j⟩} ^n_i ^n_j
```

添加 V 项(最近邻密度-密度 Coulomb 排斥)后,可以描述激子束缚:
- 纯 Hubbard (U only): 激子结合能 = 0(理论严格证明)
- 扩展 Hubbard (U+V): 一个电子跃迁到邻位时,获得结合能 V

### 3.2 PPP (Pariser-Parr-Pople) 模型

PPP 模型包含了长程 Coulomb 相互作用:

```
Ĥ_PPP = Σ_i ε_i ^n_i + Σ_{⟨i,j⟩,σ} t_{ij} (^c†_{iσ} ^c_{jσ} + h.c.)
       + Σ_i U_i ^n_{i↑} ^n_{i↓} + ½ Σ_{i≠j} γ_{ij} (^n_i - Z_i)(^n_j - Z_j)
```

**长程 Coulomb 势的插值**: \gamma_{ij} 通过经验公式从 r_{ij}=0 时的 U 平滑过渡到 r_{ij}\rightarrow\infty 时的 e^{2}/r:

**Ohno-Klopman 公式**:
```
γ_{ij} = (14.397 eV·Å) / √(a² + r_{ij}²)
a = (14.397 eV·Å) / U
```

**Mataga-Nishimoto 公式**:
```
γ_{ij} = (14.397 eV·Å) / (a + r_{ij})
```
Mataga-Nishimoto 势衰减更快。

### 3.3 PPP-Peierls (PPP-SSH) 模型

加入电子-声子耦合和键交替效应:

```
Ĥ = Ĥ_PPP + Σ_{⟨i,j⟩,σ} α (u_i - u_j) (^c†_{iσ} ^c_{jσ} + h.c.)
            + (K/2) Σ_{⟨i,j⟩} (u_i - u_j)²
```

- \alpha: 电子-声子耦合常数
- K: 键力常数
- u_i: 原子 i 的位移
- `t_{ij} = t_0 + (-1)^i \delta` 用于描述单双键交替(\delta 为 Peierls 畸变参数)

在论文 Ch.3 中,Shuai 等人证明:即使对于 PPP 这样的长程相互作用模型,DMRG 对(准)一维共轭聚合物仍保持高精度——这是量子化学 DMRG 的起点。

## 4. Holstein 模型

### 4.1 物理原理

Holstein 模型描述局域电子-声子耦合:

```
Ĥ = Σ_{ij} J_{ij} a†_i a_j + Σ_{iλ} ω_{iλ} b†_{iλ} b_{iλ}
      + Σ_{iλ} g_{iλ} ω_{iλ} a†_i a_i (b†_{iλ} + b_{iλ})
```

- **第一项**: 激子跃迁,J_{ij} 为电子耦合矩阵
- **第二项**: 局域声子(每个分子 i 有多个振动模式 \lambda,频率 \omega_{i\lambda})
- **第三项**: 局域 e-ph 耦合——a^\dagger_i a_i 表示分子 i 上的激子占据数,与声子位移 (b^\dagger+b) 耦合

**耦合强度**: 重组能 \lambda_reorg = \Sigma_{i\lambda} g_{i\lambda}^{2} \omega_{i\lambda} = \Sigma_{i\lambda} S_{i\lambda} \omega_{i\lambda},其中 S 为 Huang-Rhys 因子。

### 4.2 Renormalizer 实现

```python
from renormalizer import HolsteinModel, Mol, Phonon
from renormalizer.utils import Quantity

# 构造简谐声子
ph = Phonon.simplest_phonon(
    omega=Quantity(1500, "cm-1"),
    displacement=Quantity(1.0),       # lam=True 时: 重组能 λ (a.u.)
    temperature=Quantity(300, "K"),   # 用于确定热占据数
    lam=True                          # displacement 解释为重组能而非无量纲位移
)
# 当 lam=True: 内部 d = √(2λ)/ω 计算实际无量纲位移

mol = Mol(
    Quantity(0, "cm-1"),   # elocalex: 局域激发能(相对零点)
    [ph],                   # 声子列表
    dipole=1.0              # 跃迁偶极矩
)

model = HolsteinModel(
    mol_list=[mol] * 10,
    j_matrix=Quantity(100, "cm-1"),  # 均匀最近邻耦合 J
    scheme=2,                         # 1/2/3=交错排布, 4=集中电子
    periodic=False
)
```

### 4.3 四种 scheme 的 DoF 排列

| Scheme | 排列方式 | 用途 |
|--------|---------|------|
| 1,2,3 | [e_{0}, ph_{0}_{0}, ph_{0}_{1}, ..., e_{1}, ph_{1}_{0}, ...] | 交错排布,每个分子后跟其所有声子 |
| 4 | [ph_{0}_{0}, ph_{0}_{1}, ..., ph_{n/2,0}, ..., e_{all}, ph_{n/2+1,0}, ...] | 左侧所有声子,中间打包所有电子(BasisMultiElectronVac),右侧剩余声子 |

**注意**: Scheme 4 中每个分子的所有声子模式均被移动(不限于第0个模式)。

### 4.4 声子基组的截断

`nbas` (= `Phonon.n_phys_dim`) 控制声子 Fock 空间的截断。截断建议:

- `nbas = max(16 \times c^{2}/\omega^{3}, 4)`,其中 c 为耦合强度
- 起始试探 nbas=8-16,收敛测试增大到 nbas=16-32
- 声子占据数 `\langleb^\daggerb\rangle` 应远小于 nbas

### 4.5 Mol 和 Phonon 的完整参数

```python
# 非简谐声子 (ω₀ ≠ ω₁): 激发态势能面与基态不同
ph = Phonon(
    omega=[Quantity(1500, "cm-1"), Quantity(1450, "cm-1")],
    dis=[Quantity(0), Quantity(1.2)],   # 0 位移,1.2 激发态位移
    n_phys_dim=8
)

# 简谐声子 (ω₀ = ω₁, 线性耦合)
ph = Phonon.simplest_phonon(
    omega=Quantity(1500, "cm-1"),
    displacement=Quantity(1.0),     # lam=False: 无量纲位移; lam=True: 重组能
    lam=True
)
```

## 5. Spin-Boson 模型

### 5.1 物理原理

Spin-Boson 模型是耗散量子二能级系统的最小模型:

```
Ĥ = ε σ_z + Δ σ_x + ½ Σ_i (p_i² + ω_i² q_i²) + σ_z Σ_i c_i q_i
```

- \epsilon: 偏置能(两个能级的不对称性)
- \Delta: 隧穿矩阵元(两个能级间的耦合)
- \omega_i: 浴模式频率
- c_i: 系统和浴的耦合强度

**连续谱密度的离散化**: 实际物理系统由连续谱密度 J(\omega) 描述:
```
J(ω) = (π/2) Σ_i c_i²/ω_i δ(ω-ω_i)
```
对于一般浴: J(\omega) = (\pi/2) \alpha \omega_c (\omega/\omega_c)^s exp(-\omega/\omega_c)

- s=1: Ohmic (阻尼与频率成正比)
- s<1: sub-Ohmic (低频模式更强)
- s>1: super-Ohmic (高频模式更强)

离散化采用对数分布或线性分布,通过 n_phonons 个离散模式近似连续浴。

### 5.2 Renormalizer 实现

```python
from renormalizer import SpinBosonModel, Phonon
from renormalizer.sbm import SpinBosonDynamics, param2mollist

# 方法1: 从物理参数构建 (SpectralDensityFunction 离散化)
alpha = 0.05
delta = Quantity(1)          # a.u.
omega_c = Quantity(20)      # a.u.
n_phonons = 300

model = param2mollist(alpha, delta, omega_c,
    renormalization_p=1, n_phonons=n_phonons)
# 内部: 构造 SpinBosonModel,使用 SpectralDensityFunction 离散化

# 方法2: 手动构建
ph_list = [Phonon.simplest_phonon(Quantity(w), Quantity(d), lam=True)
           for w, d in zip(omegas, couplings)]
model = SpinBosonModel(
    epsilon=Quantity(100, "cm-1"),
    delta=Quantity(50, "cm-1"),
    ph_list=ph_list,
    dipole=1.0
)
```

### 5.3 物理参数与浴离散化的关系

| 参数 | 含义 | 典型范围 |
|------|------|---------|
| \alpha | 耦合强度 | 0.01(弱)~0.5+(强) |
| \Delta | 隧穿 | 1(参考单位) |
| \omega_c | 截止频率 | 5-50\Delta |
| n_phonons | 离散模式数 | 100(弱)~500+(强) |
| s | 谱指数 | 1(Ohmic),<1(sub-Ohmic),>1(super-Ohmic) |

## 6. Heisenberg 模型

### 6.1 物理原理

各向异性 Heisenberg 模型(物理上):
```
Ĥ_Heisenberg = Σ_i (J/2)(^S⁺_i ^S⁻_{i+1} + ^S⁻_i ^S⁺_{i+1}) + J_z ^S^z_i ^S^z_{i+1}
```

**关键**: 自旋算符与 Pauli 矩阵的关系: S^\alpha = \sigma^\alpha/2。因此:
- S^{+} S^{-} = (\sigma^{+}/2)(\sigma^{-}/2) = \sigma^{+}\sigma^{-}/4
- S^z S^z = (\sigma^z/2)(\sigma^z/2) = \sigma^z\sigma^z/4

在 Renormalizer 的 `Op` 语法中(直接使用 \sigma 算符):
```python
# 注意: Op 直接使用 σ 符号,系数需包含 1/4 因子
ham_terms = [
    Op("sigma_+ sigma_-", [i, i+1], J/4),   # = J×(S⁺S⁻)
    Op("sigma_- sigma_+", [i, i+1], J/4),   # = J×(S⁻S⁺)
    Op("sigma_z sigma_z", [i, i+1], J_z/4), # = J_z×(S^zS^z)
]
```

Renormalizer 源码 `model/model.py:heisenberg_ops` 中确实使用 `1.0/4` 作为系数。

## 7. 平移不变一维模型 (TI1DModel)

### 7.1 原理

对于具有平移对称性的一维周期系统,单胞重复 ncell 次自动构建完整的基组和哈密顿量:

```
Ĥ = Σ_i (ĥ_i + Σ_j ĥ_{i,j})
```

其中 \hat{h}_i 为局域哈密顿量,\hat{h}_{i,j} 为非局域相互作用。

### 7.2 实现

```python
from renormalizer import TI1DModel, BasisHalfSpin, Op

unit_basis = [BasisHalfSpin("e")]
local_ham = [Op("Z", "e", 1.0)]        # 每个单胞的局域项
nonlocal_ham = [
    Op("sigma_+ sigma_-", [(0,"e"), (1,"e")], 1.0),  # NN hopping
    Op("sigma_- sigma_+", [(0,"e"), (1,"e")], 1.0),  # h.c.
]

model = TI1DModel(unit_basis, local_ham, nonlocal_ham, ncell=20)
# DoF 自动扩展为 ("cell0", "e"), ("cell1", "e"), ... ("cell19", "e")
# 周期边界自动处理: (i + dist) % ncell
```

## 8. 自定义模型——直接使用 Model 基类

对于任意哈密顿量,直接使用 `Model` 基类:

```python
from renormalizer import Model, Op, BasisHalfSpin, BasisSHO

basis = [BasisHalfSpin(0), BasisSHO("v_0", ...), BasisHalfSpin(1)]
ham_terms = [
    Op("sigma_z", 0, ε) + Op("sigma_x", 0, Δ),
    Op(r"b^\dagger b", "v_0", ω) + Op("p^2", "v_0", 0.5),
    Op("sigma_z", 0) * Op("x", "v_0") * Quantity(g, "cm-1"),
]
model = Model(basis, ham_terms)
```

## 9. 单位系统与 Quantity

**关键规则**: Renormalizer 内所有数值计算使用原子单位(a.u.)。用户用 `Quantity` 声明物理量及其单位,自动转换:

```python
from renormalizer.utils import Quantity

omega = Quantity(1500, "cm-1")    # 1500 cm⁻¹
J = Quantity(100, "cm-1")
T = Quantity(300, "K")
dt = Quantity(10, "fs")

omega_au = omega.as_au()          # 自动转换为 a.u.
# 在 Op 中直接使用: Op("...", dof, factor=Quantity(100,"cm-1"))
```

**支持的 Quantity 单位**(`utils/quantity.py:au_ratio_dict`): `"meV"`, `"eV"`, `"cm^{-1}"`, `"cm-1"`, `"K"`, `"a.u."`, `"au"`, `"fs"`, `"ps"`。

**不支持的常见单位**: `"nm"`, `"\text{\AA}"` 等。使用 `utils/constant.py` 中的辅助函数手动转换(如 `nm2au`, `ang2au`)。

## 参考
- 论文 Ch.3: DMRG for semi-empirical quantum chemistry (PPP、Hubbard、SSH 模型,DMRG 对称化)
- DMRG textbook Ch.1: Second quantization, Fock space, occupation number basis
- Renormalizer 源码: `renormalizer/model/`, `renormalizer/utils/quantity.py`
- Fishman, White, Stoudenmire. SciPost Phys. Codebases 4 (2022). (ITensor MPO construction)
