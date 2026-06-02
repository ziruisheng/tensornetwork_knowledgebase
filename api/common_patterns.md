# 常见算符组合模式

## Heisenberg 自旋链
H = J \Sigma_{i} [S_{z}_{i}S_{z}_{i}_{+}_{1} + 1/2(S^{+}_{i}S^{-}_{i}_{+}_{1} + S^{-}_{i}S^{+}_{i}_{+}_{1})]
```python
ham = []
for i in range(nspin - 1):
    ham.append(Op("Z Z", [i, i+1], J/4))
    ham.append(Op("sigma_+ sigma_-", [i, i+1], J/2))
    ham.append(Op("sigma_- sigma_+", [i, i+1], J/2))
```

## 电子-声子耦合 (Holstein)
H_eph = g \Sigma a^\dagger_{i}a_{i}(b^\dagger_{i} + b_{i})
```python
ham.append(Op(r"a^\dagger a b^\dagger+b", [elec_dof, vib_dof], g))
```

## 系统-浴耦合 (Spin-Boson)
H_sb = \sigma_z \Sigma c_{i}(b^\dagger_{i} + b_{i})
```python
ham.append(Op(r"sigma_z b^\dagger+b", ["spin", f"v_{i}"], c[i]))
```
