# 非辐射衰减速率计算

## 计算流程
1. 构建 Holstein 模型 (分子聚集体)
2. 用 ThermalProp 构造有限温度热态 (如需要)
3. 用 tdvp_evolution 演化偶极-偶极时间关联函数 C(t) = \langle\mu(0)\mu(t)\rangle
4. 对 C(t) 做 FFT 得到频域光谱
5. 从光谱提取速率常数

## 关联函数 \rightarrow 速率
非辐射衰减速率:
k_nr = (1/\hbar^{2}) \int_{0}^\infty dt C(t) \times f(\omega)

其中 f(\omega) 是与非绝热耦合相关的函数。

## 简化计算
对于 Holstein 模型, 可用以下近似:
k_nr \approx (\DeltaE^{2}/\hbar) \times S(\omega_00)

其中 S(\omega_00) 是 0-0 跃迁的光谱密度。

## Tool 组合
1. holstein_model: 构建模型
2. tdvp_evolution: 演化 C(t)
3. spectrum: FFT \rightarrow 频域

## 关键参数
- evolve_time: 需足够长 (100-500 a.u.) 以分辨速率
- evolve_dt: 0.01-0.05 a.u.
- bond_dim: 32-64 (取决于耦合强度)

## 常见陷阱
- evolve_time 太短 \rightarrow 速率不收敛
- 需要检查 C(t) 是否衰减到零
- 有限温度需要更大 bond_dim
