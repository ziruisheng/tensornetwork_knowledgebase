# 频域方法: Lanczos, CV, DDMRG, CheMPS

**来源**: 论文 Ch.7 (Frequency domain DMRG), Renormalizer 源码 `renormalizer/cv/`

## 1. 谱函数的定义

线性响应理论中,谱函数(吸收截面、光电导等)的频域表达式为(T=0):

```
S(ω) = -1/π Im ⟨ψ₀|Â† (ω + E₀ - Ĥ + iη)⁻¹ Â|ψ₀⟩
```

其中:
- \hat{H}: 系统哈密顿量,|\psi_{0}\rangle, E_{0} 为其基态
- \hat{A}: 探测算符(如偶极算符 \mu,自旋算符 \sigma^z)
- \eta: Lorentz 展宽参数(数值上避免极点,物理上对应谱线宽度)

**与含时方法的等价性**: 谱函数是时间关联函数 C(t) = \langle\psi_{0}|\hat{A}^\dagger(t) \hat{A}(0)|\psi_{0}\rangle 的 Fourier 变换:
```
S(ω) = 1/2π ∫_{-∞}^{∞} C(t) e^{iωt} dt
```
时域方法和频域方法是同一问题的两个互补路径。

## 2. Lanczos DMRG

### 2.1 原理

Lanczos 算法将大型稀疏矩阵 \hat{H} 投影到 Krylov 子空间:

```
K_N = span{|v₀⟩, Ĥ|v₀⟩, Ĥ²|v₀⟩, ..., Ĥ^{N-1}|v₀⟩}
```

其中 |v_{0}\rangle = \hat{A}|\psi_{0}\rangle。在 Krylov 基的正交基下,\hat{H} 约化为三对角矩阵 T_N:

```
        [α₁  β₁  0   ··· ]
T_N =   [β₁  α₂  β₂  ··· ]
        [0   β₂  α₃  ··· ]
        [··· ··· ··· ···  ]
```

**Lanczos 三项递推**(用正交的 Krylov 基矢量 {|v_n\rangle}):
```
β_{n+1} |v_{n+1}⟩ = Ĥ |v_n⟩ - α_n |v_n⟩ - β_n |v_{n-1}⟩
其中: α_n = ⟨v_n|Ĥ|v_n⟩
      β_n = ⟨v_{n-1}|Ĥ|v_n⟩
```

### 2.2 连续分数展开(CFE)

不直接对角化 T_N,而是通过连续分数得到平滑谱线:

```
G(ω) ≈ ⟨e₁|(ω - T_N)⁻¹|e₁⟩ = 1 / (ω - α₁ - β₁² / (ω - α₂ - β₂² / (ω - α₃ - β₃² / (...))))
```

截断到 N 项,加上 Lorentz 展宽 \eta \rightarrow 平滑谱函数。

### 2.3 MPS/MPO 方案的 Lanczos DMRG

在现代框架中:
1. \hat{H} 精确表示为 MPO,|v_{0}\rangle = \hat{A}|\psi_{0}\rangle 为 MPS
2. 递推: |v_{n+1}\rangle = \hat{H}|v_n\rangle - \alpha_n|v_n\rangle - \beta_n|v_{n-1}\rangle
   - \hat{H}|v_n\rangle: MPO contract,MPS 加法,压缩
3. **正交性丧失**: MPS 压缩破坏 Lanczos 矢量正交性 \rightarrow "鬼"本征值
4. **Gram-Schmidt 重正交化**: 用内积 \langlev_i|v_j\rangle 构建系数矩阵 C,重新正交化:
   ```
   |v'_i⟩ = Σ_j C_{ij} |v_j⟩
   ```
   然后在重正交化基下构造有效 H_eff_{ij} = \langlev'_i|\hat{H}|v'_j\rangle,对角化得能量和谱权重。

### 2.4 局限性
- CFE 只能得到**离散谱**(前几个激发态的 delta 峰 + 展宽)
- 多 targeted 方案: 约化密度矩阵来自所有目标态的平均 \rightarrow 每个态精度随目标态数量增加而下降

## 3. 修正矢量 (Correction Vector) / DDMRG

### 3.1 原理

直接在频域求解修正矢量 |x(\omega)\rangle:
```
(ω + E₀ + iη - Ĥ) |x(ω)⟩ = Â|ψ₀⟩
```

**DDMRG 变分表述**: 不直接解线性系统,而是变分最小化:
```
min ||(ω + E₀ - Ĥ + iη) |x(ω)⟩ - |b⟩||²
```

等价于求解法方程(normal equations):
```
[(Ĥ - E₀ - ω)² + η²] |x⟩ = -η |b⟩
```

该矩阵实对称正定,在 DMRG sweep 内用**共轭梯度(CG)** 求解。每个频率点独立计算。

### 3.2 两个方法的统一

**CV-DMRG 和 DDMRG 在 Renormalizer 中是同一个实现**——`SpectraZtCV` 类的 docstring 明确说明"Use DDMRG to calculate the zero temperature spectrum"。概念上:
- "CV-DMRG" 强调通过修正矢量求解线性响应问题
- "DDMRG" 强调变分优化和 DMRG sweep 过程
- 在同一代码中,两者等价:通过法方程+CG在DMRG sweep中求解

### 3.3 Algorithm 流程

1. 对每个目标频率 \omega:
   a. 构造源项 |b\rangle = -\eta \hat{A}|\psi_{0}\rangle
   b. 在 DMRG sweep 中,用 CG 迭代解法方程
   c. sweep 收敛后得修正矢量 |x(\omega)\rangle
   d. S(\omega) = -1/\pi Im \langle\psi_{0}|\hat{A}^\dagger|x(\omega)\rangle

## 4. Chebyshev MPS (CheMPS)

### 4.1 原理

谱函数展宽形式:
```
S(ω) = Σ_n |⟨ψ_n|Â|ψ₀⟩|² δ(ω - (E_n-E₀))
```

引入 Chebyshev 多项式展开:
```
S(ω) ≈ 1/(π√(1-ω̄²)) [μ₀ + 2 Σ_{n=1}^{N} μ_n T_n(ω̄)]
```

其中 \bar{\omega} = (\omega - a)/b 是将谱范围 [E_min, E_max] 重标定到 [-1,1] 的仿射变换。

**Chebyshev 矩**: \mu_n = \langle\psi_{0}|\hat{A}^\dagger T_n(\hat{H}') \hat{A}|\psi_{0}\rangle,其中 \hat{H}' = (\hat{H} - a)/b。

**Chebyshev 递推**:
```
T₀ = 1, T₁ = Ĥ'
|t_{n+1}⟩ = 2Ĥ'|t_n⟩ - |t_{n-1}⟩
```
其中 |t_n\rangle = T_n(\hat{H}') \hat{A}|\psi_{0}\rangle,通过 MPO 作用 + MPS 加法 \pm 压缩计算。

### 4.2 优势与局限

| 优势 | 局限 |
|------|------|
| 一次计算获得全频率谱 | 需 N_Cheb ~ 100-1000 次递推 |
| 精度随 N_Cheb 指数收敛 | 递推中 MPS 误差累积 |
| 可以与热场动力学结合(有限温度) | 线性预测外推可能引入伪峰 |

## 5. Renormalizer 中的频域实现——CV 模块

Renormalizer 通过 `cv/` 模块实现 DDMRG:

```python
from renormalizer.cv import batch_run
from renormalizer.cv.zerot import SpectraZtCV
from renormalizer.cv.finitet import SpectraFtCV

# 零温 DDMRG
spec = SpectraZtCV(
    model,
    spectratype="abs",        # "abs" 或 "emi"
    m_max=32,                 # 修正矢量最大键维度
    eta=0.01,                 # Lorentz 展宽 (a.u.)
    method="2site",           # DMRG sweep 方式
    procedure_cv=[[32, 0.2], [32, 0.1], [32, 0], [32, 0]],
    rtol=1e-5,
)

# 逐频率求解
freq_au = np.linspace(0, 0.5, 100)  # 频率范围 (a.u.)
spectra = []
for omega in freq_au:
    spectra.append(spec.cv_solve(omega))

# 并行批量求解
spectra = batch_run(freq_au.tolist(), cores=4, obj=spec)
```

**有限温度 DDMRG**:
```python
from renormalizer.cv.finitet import SpectraFtCV
from renormalizer.utils import CompressConfig, EvolveConfig, Quantity

spec_ft = SpectraFtCV(
    model,
    spectratype="abs",
    m_max=32,
    eta=0.01,
    temperature=Quantity(300, "K"),
    icompress_config=CompressConfig(threshold=1e-4),
    ievolve_config=EvolveConfig(guess_dt=1e-3/1j),
    insteps=300,                   # 虚时间步数
)

spectra = batch_run(freq_au.tolist(), cores=4, obj=spec_ft)
```

**工作流**: 两个阶段:
1. **虚时间传播**: 用 `ThermalProp` 从 T=\infty 冷却到目标温度,构造热密度矩阵
2. **频率扫描**: 对每个 \omega,在热密度矩阵构型上用 CG 求解 DDMRG 法方程

## 6. 频域 vs 时域方法的选择

| 场景 | 推荐方法 | Renormalizer 模块 |
|------|---------|-------------------|
| 宽频率范围,高分辨率 | CheMPS | 未内置 |
| 少数特定频率,高精度 | DDMRG | `cv/` (SpectraZtCV, SpectraFtCV) |
| 含时过程感兴趣 | TD-DMRG | `spectra/` (SpectraZeroT, SpectraFiniteT) |
| 复杂声子谱,大系统 | DDMRG + CV | `cv/` |

**注意**: `spectra/` 模块中的 `SpectraZeroT`/`SpectraFiniteT` 是**时域方法**(实时间 TD-DMRG + Fourier 变换),不属于频域范畴。

## 7. 有限温度谱函数(物理上)

```
S(ω) = Σ_{n,m} (ρ_n - ρ_m) |⟨m|Â|n⟩|² δ(ω - (E_m - E_n))
```

其中 \rho_n = exp(-\betaE_n)/Z。第一项(\rho_n \times \delta)代表吸收,第二项(\rho_m \times \delta)代表受激发射。在低温下(\hbar\omega \gg k_B T),\rho_m \approx 0,受激发射项可忽略。

## 参考
- 论文 Ch.7: Frequency domain DMRG (Lanczos, CV, DDMRG, CheMPS)
- Jeckelmann, PRB 66, 045114 (2002): DDMRG
- Holzner et al. PRB 83, 195115 (2011): CheMPS
- Renormalizer 源码: `renormalizer/cv/zerot.py` (SpectraZtCV), `renormalizer/cv/finitet.py` (SpectraFtCV)
