# 量子数守恒规则

## 设置位置
1. 基组 sigmaqn: 定义每个局域态的量子数
2. 算符 qn: 定义算符作用时的量子数变化
3. MPS qntot: 目标总量子数

## 常见守恒模式
- 自旋投影: qntot=0
- 粒子数: sigmaqn=[0,1], qntot=nelec
- 多个守恒量: sigmaqn=[[0,0],[1,0]], qntot=[n_up, n_down]

## 禁用量子数
不需要守恒时必须显式禁用:
```python
BasisMultiElectron([0, 1], sigmaqn=[0, 0])
Op(..., qn=[0, 0])
```
否则结果可能错误。
