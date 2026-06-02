# Spin-Boson 模型

## 哈密顿量
H = \epsilon\sigma_z + \Delta\sigma_x + \Sigma \omega_{i}b^\dagger_{i}b_{i} + \Sigma c_{i}\sigma_z(b^\dagger_{i} + b_{i})

## 基组
- BasisHalfSpin("spin") 系统部分
- BasisSHO(f"v_{i}", omega[i], nbas[i]) 浴部分

## 便捷工具
直接使用 spin_boson_dynamics 工具,只需提供 alpha, delta, omega_c 参数。

## 典型参数
- alpha=0.05 (弱耦合) 到 0.5 (强耦合)
- delta=1, omega_c=20
- n_phonons=300
- evolve_time=20
