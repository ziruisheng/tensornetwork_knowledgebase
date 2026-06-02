# Kubo 公式输运计算

## 物理背景
载流子迁移率 \mu 通过 Kubo (线性响应) 公式计算:

\mu = (1/kT) \int_{0}^\infty dt \langlej(t) j(0)\rangle

其中 j 是电流算符, \langle...\rangle 表示热平衡平均。

## 两种计算框架

### 1. TransportKubo (推荐)
Renormalizer 内置的 Kubo 迁移率计算类。
- 输入: HolsteinModel + 温度
- 内部自动处理: 热态构造 (ThermalProp) + TDVP 演化 + 关联函数积分
- 工具: transport_kubo

### 2. 手动计算
通过 tdvp_evolution + spectrum 工具组合:
1. 用 ThermalProp 构造有限温度热态
2. 用 tdvp_evolution 演化电流算符作用后的态
3. 计算 \langlej(t)j(0)\rangle 关联函数
4. 积分得到迁移率

## 电流算符
对于 Holstein 模型:
j = i \Sigma_ij J_ij (a^\dagger_i a_j - a^\dagger_j a_i)

在 MPS 框架中, 电流算符是 Mpo 对象。

## 温度效应
- 零温: 只有基态贡献, 适用于低温极限
- 有限温度: 需要 ThermalProp (虚时演化) 构造热态
- TransportKubo 内部自动处理有限温度

## 关键参数
- temperature: 必须 > 0 (Kubo 公式需要热态)
- evolve_time: 需足够长以积分关联函数 (通常 100-500 a.u.)
- bond_dim: 高温可小 (M=16-32), 低温需大 (M=64-128)

## Tool 映射
- transport_kubo: 直接计算迁移率
- charge_transport: 计算均方位移 (扩散系数)
- transport: 通用输运 (需手动构建算符)

## 常见陷阱
- temperature=0 会导致 Kubo 公式发散
- evolve_time 太短会导致积分不收敛
- bond_dim 不足会导致关联函数振荡
