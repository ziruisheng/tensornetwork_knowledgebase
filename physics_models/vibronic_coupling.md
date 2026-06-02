# 振动耦合与 Duschinsky 旋转

## 物理背景
分子电子态之间的振动耦合 (vibronic coupling) 描述:
- 电子激发时势能面的位移 (Huang-Rhys 效应)
- 不同电子态的振动模之间的混合 (Duschinsky 旋转)

## Duschinsky 旋转
激发态的振动坐标 Q' 与基态的振动坐标 Q 的关系:
Q' = U Q + d

其中 U 是 Duschinsky 矩阵 (旋转), d 是位移矢量。

## 物理效应
- Franck-Condon 因子: 振动重叠积分
- Herzberg-Teller 效应: 电子跃迁偶极随振动坐标变化
- 振动分辨光谱: 吸收/荧光的精细结构

## 在 DMRG 中的处理
Duschinsky 旋转使不同振动模耦合, 需要:
1. 将势能面展开为 SOP (sum-of-product) 形式
2. 使用 BasisSHO 或 BasisSineDVR 构建振动基
3. 通过 Op 组合构建耦合项

## 算符构建示例
```python
# Duschinsky 耦合项
Op("b^\dagger+b b^\dagger+b", [mode_i, mode_j], coupling_ij)
```

## Tool 映射
- 模型构建: general_model (model_script)
- 光谱计算: spectrum (FCF/TVCF 方法)
- 基态搜索: dmrg_ground_state
- 时间演化: tdvp_evolution

## 关键参数
- Huang-Rhys 因子 g^{2}: 描述势能面位移
- Duschinsky 角 \theta: 描述振动模混合程度
- 振动频率比 \omega'/\omega: 激发态与基态频率差异

## 常见陷阱
- Duschinsky 旋转使 DOF 排序重要性增加
- Huang-Rhys 因子大时需要更多声子截断
- 频率比远离 1 时需要更大基组
