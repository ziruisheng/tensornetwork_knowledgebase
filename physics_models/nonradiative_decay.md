# 非辐射衰减与能隙定律

## 物理背景
分子从激发态回到基态有两种途径:
1. 辐射跃迁 (荧光/磷光): 发射光子
2. 非辐射跃迁: 能量转化为振动 (热)

非辐射衰减速率 k_nr 由能隙定律 (Energy Gap Law) 描述:
k_nr \propto exp(-\gamma \DeltaE/\hbar\omega)

其中 \DeltaE 是能隙, \omega 是有效振动频率, \gamma 是与耦合强度相关的常数。

## 能隙定律 vs 激子离域
传统能隙定律预测: 能隙越小, k_nr 越大 (指数增长)。
但分子聚集态中, 激子离域可对抗能隙定律:
- 激子耦合 J 缩小能隙 \rightarrow 加速非辐射衰减
- 激子耦合 J 降低有效电子-声子耦合 \rightarrow 抑制非辐射衰减
- 两种效应竞争, 存在最优 |J| \approx 0.5\lambda

## 计算方法
通过时间关联函数 (TCF) 计算:

k_nr = (1/\hbar^{2}) \int_{0}^\infty dt C(t)
C(t) = \langle\DeltaV(0) \DeltaV(t)\rangle

其中 \DeltaV 是非绝热耦合算符 (势能面差 \times 振动坐标)。

## Tool 映射
- 模型构建: holstein_model (聚集体) 或 general_model (自定义)
- 时间演化: tdvp_evolution (计算 TCF)
- 光谱/速率: spectrum (TCF \rightarrow FFT \rightarrow 频域)
- 有限温度: ThermalProp (热平衡初态)

## 典型参数
- 激子耦合 J: 0.01-0.1 eV
- 声子频率 \omega: 0.01-0.2 eV
- 能隙 \DeltaE: 0.5-3 eV (近红外: 0.5-1.5 eV)
- Huang-Rhys 因子: 0.1-2

## 关键洞察 (来自论文)
- 03_NatComm_2023: |J|\approx0.5\lambda 时 k_nr 最小
- zhang2025effect: 动态无序 (分子间振动) 对 k_nr 有显著影响
- 09_JCP_2021: 非谐效应使 k_nr 增大 4.5 倍
