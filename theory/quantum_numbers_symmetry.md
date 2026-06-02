# 量子数守恒与对称化DMRG

**来源**: 论文 Ch.3 (Symmetrized DMRG 算法), DMRG textbook Ch.2, Renormalizer 源码 `mps/svd_qn.py`

## 1. 量子数守恒的物理基础

对于非相对论封闭量子系统,以下物理量守恒:
- **总粒子数 N** = \Sigma_i ^n_i (U(1) 对称性)
- **总自旋 z-分量 S_z** (U(1) 对称性)
- **总自旋 S^{2}** (SU(2) 对称性,非Abel群)

**Renormalizer 实现范围**: Renormalizer 仅实现 **U(1) 交换量子数**(可加的 Abel 对称性)。多个 U(1) 量子数可以组合为高维量子数向量(如 [N_\alpha, N_\beta],即 `qn_size=2`),但 SU(2) 非 Abel 对称性不在当前实现范围内。

存在守恒量时,哈密顿量和约化密度矩阵在守恒量子数的不同子空间之间**分块对角**(block-diagonal),由此产生:

1. **稀疏性**: 张量矩阵元中大量为零元素 \rightarrow 压缩存储,减少内存和计算
2. **子空间选择**: 可以通过设定总量子数 qntot 直接 target 特定激子数/自旋的态
3. **截断效率**: 在每个量子数块内独立截断,避免混合不同对称性

## 2. Renormalizer 的量子数框架

### 2.1 局域量子数 sigmaqn

每个 `BasisSet` 定义了 `sigmaqn`: 形状为 `(nbas, qn_size)` 的数组,每行是每个局域基矢的量子数向量。

```python
# BasisHalfSpin: 默认 sigmaqn=[[0],[0]] (自旋向上和向下均携带量子数 0)
#   用户需显式传入 sigmaqn=[[0],[1]] 来追踪 S_z
# BasisSimpleElectron: sigmaqn=[[0],[1]] (默认: 0电子→0, 1电子→+1)
# BasisSHO: sigmaqn=[[0],[0],...] (所有声子态具有相同量子数,因为是玻色子)
```

**源码位置**: `renormalizer/model/basis.py` 中每个 BasisSet 子类的 `sigmaqn` 属性。

### 2.2 模型级别的量子数一致性

`Model.__init__` 验证所有基组的 `sigmaqn` 具有相同的列数:

```python
# model.py: Model.__init__
qn_size_list = [b.sigmaqn.shape[1] for b in basis]
if len(set(qn_size_list)) != 1:
    raise ValueError(f"Inconsistent quantum number size: {set(qn_size_list)}")
self.qn_size: int = qn_size_list[0]
```

- `qn_size=0`: 无量子数守恒(对于无守恒量的系统)
- `qn_size=1`: 一维量子数(如总 N 或总 S_z, U(1) 对称性)
- `qn_size=2`: 二维量子数(如 [N_alpha, N_beta] 独立守恒,两个 U(1))

### 2.3 算符的量子数

`Op` 中的 `qn_list` 定义了每个符号的量子数变化:

```python
# 默认规则: a† → +1, a → -1, 其他 → 0
# 注意: 必须使用关键字参数 qn=... 传递
Op(r"a^\dagger a", [0, 1], qn=[1, -1])   # qn = [+1, -1] = 0 (粒子数不变)

# 显式量子数:
Op("sigma_+", 0, qn=-1)     # σ⁺|⟩→|⟩ Renormalizer约定: ΔS_z = -1
Op("sigma_-", 0, qn=+1)     # σ⁻|⟩→|⟩ Renormalizer约定: ΔS_z = +1

# 多向量子数 (Hubbard模型):
Op("- + - +", dofs, qn=[[1,0], [-1,0], [0,1], [0,-1]], factor=U)
```

**重要**: `Model.check_operator_terms` 只做三项检查: (1)算符类型为 Op/OpSum,(2)所有 DoF 在基组中存在,(3)去掉零系数的项。**不自动验证算符链的总量子数为零**——这是用户的责任。

## 3. Block-Sparse 张量操作

### 3.1 量子数传播

在 MPS 中,键 i 处的基矢携带量子数,通过 add_outer 计算下一个键的量子数:

```python
# svd_qn.py: add_outer
# qn[i+1] = add_outer(qn[i], sigmaqn[i])
# 将 qn[i] (D_left, qn_size) 与 sigmaqn[i] (d, qn_size) 的所有组合相加
# 得到 (D_left × d, qn_size) 的量子数集合
```

**过滤**: `get_qn_mask(qnmat, qntot)` 只保留量子数**精确等于** qntot 的组合:

```python
# mps/svd_qn.py: get_qn_mask
def get_qn_mask(qnmat, qntot):
    return np.all(qnmat == np.array(qntot), axis=-1)
```

### 3.2 Block-Sparse SVD

`svd_qn.svd_qn` 是量子数感知的 SVD——不是对整矩阵做 SVD,而是:

1. 按 (qn_left, qn_right) 将矩阵分块
2. 每个块独立做 SVD
3. 基变换矩阵(左/右奇异矢量)保持在量子数基下

```python
# svd_qn.py: svd_qn(coef_array, qnlset, qnrset, qntot, ...)
# 其中 qnlset 和 qnrset 是矩阵行和列的量子数集合
# 返回: (U, S_u, new_qnl, V, S_v, new_qnr)   (当 QR=False, 默认)
# 或: (u, new_qnl, v, new_qnr)              (当 QR=True)
```

### 3.3 量子数块内的截断

`CompressConfig.compute_m_trunc` 在量子数感知的上下文中工作——截断在每个量子数块内按奇异值大小独立进行:

- **fixed 模式**: 所有块中按奇异值大小排序,最多保留 max_bonddim 个
- **threshold 模式**: 每个块独立丢弃 < threshold 的奇异值

## 4. 对称化 DMRG——论文 Ch.3 的贡献

### 4.1 粒子数与 S_z 对称性 (U(1)\timesU(1))

**实现**: 每个 renormalized 基矢携带好量子数 (N, S_z)。约化密度矩阵在 (N, S_z) 块下完全对角,截断自然保持对称性。Renormalizer 中这是默认行为——所有 MPS 操作都是量子数感知的。

### 4.2 自旋翻转对称性

对于 S_z=0 的子空间,自旋翻转算符 ^F = \Pi_i ^f_i (其中 ^f_i|\uparrow\rangle = |\downarrow\rangle, ^f_i|\downarrow\rangle = |\uparrow\rangle) 将系统分为偶(e)和奇(o)两个不可约表示:
- 偶态(e): S = 0, 2, 4, ...
- 奇态(o): S = 1, 3, 5, ...

通过在 DMRG 过程中跟踪自旋翻转对称性,可以区分不同总自旋的态,即使 S 不守恒时也成立。

**在 Renormalizer 中**: 通过在自定义 sigmaqn 中编码实现——用户可以将多个量子数维度组合成一个高维量子数向量,同时追踪 N, S_z 和自旋翻转奇偶性。

### 4.3 空间对称性 (C2h/C2v 点群)

对于具有空间对称性的分子(如反式聚乙炔的 C2h),可以利用对称性区分不同不可约表示的态。

**实现方法**(论文 Ch.3,Sec.3.3):
- 在实空间中,DMRG sweep 到链中心时应用对称算符
- L-block 和 R-block 的基互为镜像(对称操作下)

**Renormalizer 的处理**: 通过自定义 Op 算符(例如仅构造 Ag 对称或 Bu 对称的哈密顿量项)来手动限制对称子空间。Renormalizer 不自动检测点群对称性。

### 4.4 电子-空穴对称性

在 bipartite 晶格(如 polyacetylene)且半填充时,存在电子-空穴变换对称性:

```
^c_{iσ} → (-1)^i ^c†_{iσ}   (粒子↔空穴 + 子晶格交错符号)
```

**在 Renormalizer 中**: 不作为内置对称性,但用户在构造哈密顿量时可以通过只保留偶(或奇)电子-空穴宇称的算符项来实现。

## 5. "单格点陷阱"——形式化的理解

### 5.1 为什么量子数分布会"冻结"

考虑混合正则 MPS 中 \ell 处的 SVD 截断:
```
M^{σ_ℓ} → SVD → A^{σ_ℓ} · S · (B^{σ_{ℓ+1}})^T
```

截断后,由于 SVD 在每个量子数块内独立进行,同一量子数块的基向量的个数在截断后保持不变。如果初始态的量子数分布与真实基态不同,在整个 sweep 中永远无法改变 \rightarrow 局部极小。

**更广义的陷阱**: 即使不使用量子数守恒,单格点算法也不能扩充键基空间(键维度只能通过 SVD 截断减少或保持不变,不能增加新的独立键方向)——这是困扰所有 1-site DMRG 变体的普遍问题。

### 5.2 解决方案总结

| 方案 | 机制 | Renormalizer 对应 |
|------|------|-------------------|
| 双格点 DMRG | 两格点合并后 SVD 维度更大 \rightarrow 可重分布 | `method="2site"` |
| 密度矩阵扰动 (White 2005) | 在 \rho_A 中加入连接项 | — |
| 子空间扩展 (Hubig 2015) | 显式扩展键基子空间 | — |
| 噪声混合 | 在截断的密度矩阵中人为均匀混合 | `OptimizeConfig.procedure` 中的 `percent` |

**Renormalizer 的噪声机制**: `procedure=[[M, 0.4], [M, 0.3], [M, 0.1], [M, 0], ...]` 中第二个值控制 `select_basis` 函数在每个量子数块中平均分配的基矢比例(与奇异值大小无关),高噪声允许探索更大的量子数分布空间;随着 sweep 进行,噪声归零确保收敛到严格极小。

## 6. On-the-Fly Swapping (OFS)

### 6.1 原理

MPS 的 DoF 顺序对纠缠和键维度有重大影响。OFS 在 DMRG sweep 过程中动态交换相邻 DoF 的位置:

```
[A^{σ_i}] — [A^{σ_{i+1}}] → 收缩 → SVD → 交换物理指标顺序 → 重组
```

交换后,如果纠缠降低(根据 `OFS` 的判据),则保留新顺序;否则回退。

### 6.2 Renormalizer 实现

```python
from renormalizer.utils import CompressConfig, CompressCriteria, OFS

CompressConfig(
    criteria=CompressCriteria.both,
    max_bonddim=64, threshold=1e-3,
    ofs=OFS.ofs_ds            # 混合判据 (纠缠熵+丢弃权重)
)
```

| OFS 方法 | 判据 | 适用 |
|----------|------|------|
| `ofs_s` = "OFS-S" | 纠缠熵 | 一般用途 |
| `ofs_d` = "OFS-D" | 丢弃权重 | 高精度截断 |
| `ofs_ds` = "OFS-D/S" | 混合 | 两者兼顾 |

**注意**: OFS 默认关闭 (`ofs=None`)。启用 OFS 后,`mps.model.basis` 的顺序会改变,需要重新构建 MPO 以匹配新顺序。

## 7. Renormalizer 中量子数的完整生命周期

```
BasisSet.sigmaqn ──→ Model.qn_size (验证一致性)
       │
       ▼
Op.qn_list ──→ Model.check_operator_terms (检查DoF存在,不检查量子数总和)
       │
       ▼
Mps.random / Mps.hartree_product_state (qntot 参数)
       │
       ▼
svd_qn.svd_qn (block-sparse SVD) → get_qn_mask (精确相等过滤)
       │
       ▼
CompressConfig.compute_m_trunc (分块截断)
       │
       ▼
optimize_mps / mps.evolve (sweep 中持续使用)
```

## 参考
- 论文 Ch.3 Sec.3: Symmetrized DMRG algorithm (粒子数、自旋翻转、空间、电子-空穴对称性)
- Schollwoeck (2011) Sec.7: Exploitation of symmetries
- McCulloch & Gulacsi (2002): The non-Abelian density matrix renormalization group algorithm (SU(2) DMRG — 文献参考,非 Renormalizer 实现)
- Renormalizer 源码: `mps/svd_qn.py`, `model/basis.py`, `utils/configs.py`
