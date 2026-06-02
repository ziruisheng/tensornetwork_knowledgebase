# 分子结量子输运

## 物理背景
分子结是纳米尺度的电荷传输器件:
金属电极 - 分子 - 金属电极

电子通过分子桥在两个电极间传输, 受分子振动 (声子) 调制。

## 哈密顿量
H = H_mol + H_L + H_R + H_coup

- H_mol: 分子哈密顿量 (电子 + 声子)
- H_L, H_R: 左右引线 (费米子浴)
- H_coup: 分子-引线耦合

## 引线建模
引线通常建模为半无限链:
H_lead = \Sigma_k \epsilon_k c^\dagger_k c_k + \Sigma_k t_k (c^\dagger_k c_{k+1} + h.c.)

在 DMRG 中, 引线被截断为有限链 (Wilson 链形式)。

## 谱密度
引线的影响通过谱密度 J(\omega) 描述:
J(\omega) = 2\pi \Sigma_k |t_k|^{2} \delta(\omega - \epsilon_k)

常见形式:
- Ohmic: J(\omega) = 2\alpha\omega_c^(1-s) \omega^s exp(-\omega/\omega_c)
- Debye: J(\omega) = 2\alpha\omega\omega_c / (\omega^{2} + \omega_c^{2})

## 计算方法
1. 零温猝灭动力学: 初始态为左右引线占据数不同的态
2. 有限温度: 热 Bogoliubov 变换或 ThermalProp
3. 稳态电流: 从暂态演化中提取

## Tool 映射
- 模型构建: general_model (需手动构建引线 + 分子 + 耦合)
- 时间演化: tdvp_evolution
- 输运计算: transport

## 关键参数
- 引线耦合 \Gamma: 分子-引线杂化强度
- 偏压 V: 左右化学势差
- 电子-声子耦合 \lambda: 分子内振动
- 温度 T: 热涨落

## 常见陷阱
- 引线截断长度需收敛测试
- Wilson 链的离散化参数影响精度
- 有限温度需要额外处理 (热 Bogoliubov 或 ThermalProp)
