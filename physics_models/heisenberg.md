# Heisenberg 自旋链

## 哈密顿量
H = J \Sigma_{i} [S_{z}_{i}S_{z}_{i}_{+}_{1} + 1/2(S^{+}_{i}S^{-}_{i}_{+}_{1} + S^{-}_{i}S^{+}_{i}_{+}_{1})]

## 基组
- BasisHalfSpin, 每个位点一个
- 必须用 Op 对象构建哈密顿量，不能用 tuple

## 量子数
- qntot=0 (总自旋投影守恒)

## 典型参数
- J=1 (反铁磁)
- nsites=10-100
- bond_dim=30-100

## 参考能量
- 4 sites, M=16: E \approx -1.618034 a.u. (精确解 -2(1-cos(\pi/5)) \approx -1.618034)
- 32 sites, M=30: E/J \approx -13.997

## model_script 示例 (dmrg_ground_state)

```python
from renormalizer import BasisHalfSpin, Op, Model

nsites = 4
basis = [BasisHalfSpin(i) for i in range(nsites)]
ham = []
for i in range(nsites - 1):
    ham.append(Op("Z Z", [i, i+1], 0.25))
    ham.append(Op("sigma_+ sigma_-", [i, i+1], 0.5))
    ham.append(Op("sigma_- sigma_+", [i, i+1], 0.5))
model = Model(basis, ham)
qntot = 0
```

## 关键规则
- 必须用 `Op(symbol, dof_list, factor)` 构建算符
- 符号用空格分隔: "Z Z" 不是 "ZZ"
- J 反铁磁时 factor 为正 (H = J S\cdotS, J>0)
- `sigma_+` 和 `sigma_-` 是正确的符号，不是 `sp`/`sm`
