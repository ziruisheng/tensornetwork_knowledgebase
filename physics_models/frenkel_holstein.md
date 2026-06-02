# Frenkel-Holstein 模型 (分子聚集体)

## 哈密顿量
H = \Sigma_ij J_ij a^\dagger_i a_j + \Sigma_i\lambda \omega_i\lambda b^\dagger_i\lambda b_i\lambda + \Sigma_i\lambda g_i\lambda \omega_i\lambda a^\dagger_i a_i (b^\dagger_i\lambda + b_i\lambda)

- J_ij: 激子耦合 (分子间转移积分), J<0 为 J 聚集体, J>0 为 H 聚集体
- \omega_i\lambda: 第 i 个分子第 \lambda 个声子模的频率
- g_i\lambda: 无量纲电子-声子耦合强度, g = sqrt(\lambda/\omega), \lambda 为重组能

## 与 Hubbard/Holstein 模型的区别
- Frenkel-Holstein: 激子数守恒, 通常 qntot=1 (单激子激发)
- Hubbard: 电子数守恒, qntot=n_electrons
- Holstein 模型工具默认 qntot=1, 适合分子聚集体

## 基组
- 激子自由度: BasisSimpleElectron (二态电子, |g\rangle 和 |e\rangle)
- 声子自由度: BasisSHO (谐振子基)

## 典型应用场景
- J/H 聚集体的吸收/荧光光谱
- FMO 等光合天线复合物的激子传递
- 超辐射/亚辐射现象
- 激子相干长度 (ECL) 计算

## Tool 映射
- 单声子模: holstein_model (参数: j_coupling, ph_omega, ph_lambda)
- 多声子模: holstein_model (参数: ph_omega_list, ph_lambda_list, n_phonons_list)
- 自定义模型: general_model (model_script)
- 光谱: spectrum (偶极关联函数 FFT)
- 动力学: tdvp_evolution (激子布居演化)

## 关键参数选择
- 激子耦合 J: 通常 0.01-0.1 eV
- 声子频率 \omega: 通常 0.01-0.2 eV (分子内振动)
- Huang-Rhys 因子 g^{2}: 通常 0.1-2
- bond dimension: 强耦合需 M\geq64, 弱耦合 M=16-32

## 常见陷阱
- qntot 必须设为 1 (单激子), 不是 nsites//2
- 声子截断数需检查: 占据数 < n_phonons-2 才算收敛
- J 聚集体 (J<0) 和 H 聚集体 (J>0) 的光谱特征完全不同
