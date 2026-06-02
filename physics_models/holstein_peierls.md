# Holstein-Peierls 模型 (非局域电子-声子耦合)

## 哈密顿量
H = \Sigma_ij J_ij a^\dagger_i a_j + \Sigma_i\lambda \omega_\lambda b^\dagger_i\lambda b_i\lambda
  + \Sigma_i\lambda g_\lambda \omega_\lambda a^\dagger_i a_i (b^\dagger_i\lambda + b_i\lambda)        [Holstein: 局域耦合]
  + \Sigma_<ij>\lambda g_P \omega_\lambda a^\dagger_i a_j (b^\dagger_i\lambda + b_i\lambda)      [Peierls: 非局域耦合]

Peierls 耦合表示转移积分 J_ij 受分子间振动调制。

## 与纯 Holstein 模型的区别
- Holstein: 声子只影响位点能量 (对角项)
- Peierls: 声子影响转移积分 (非对角项)
- 两者共存时称为 Holstein-Peierls 模型

## 物理效应
- Peierls 耦合可导致声子辅助输运 (phonon-assisted transport)
- 强 Peierls 耦合可导致瞬态局域化 (transient localization)
- 对 Seebeck 系数影响较小 (相消效应), 对电导率影响显著

## 构建方法
需要使用 general_model 工具, 手动构建 Op:

```python
from renormalizer import BasisSimpleElectron, BasisSHO, Op, Model

basis = []
ham = []
for i in range(nsites):
    basis.append(BasisSimpleElectron(i))
    basis.append(BasisSHO(f"v_{i}", omega, nbas))

# Holstein 耦合 (局域)
for i in range(nsites):
    ham.append(Op(r"a^\dagger a b^\dagger+b", [i, f"v_{i}"], g_holstein))

# Peierls 耦合 (非局域)
for i in range(nsites - 1):
    ham.append(Op(r"a^\dagger a b^\dagger+b", [i, f"v_{i+1}"], g_peierls))
    ham.append(Op(r"a^\dagger a b^\dagger+b", [i+1, f"v_{i}"], g_peierls))
```

## Tool 映射
- 一般用 general_model (model_script 构建完整哈密顿量)
- 电荷输运: transport_kubo (需修改模板支持 Peierls)
- 热电输运: transport

## 典型参数 (有机半导体)
- Holstein 耦合 g_H: sqrt(\lambda_H/\omega), \lambda_H \approx 0.01-0.1 eV
- Peierls 耦合 g_P: 通常比 g_H 小 2-5 倍
- 声子频率 \omega: 0.01-0.2 eV
