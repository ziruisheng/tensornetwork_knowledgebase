# 量子数陷阱

## 陷阱 1: 不需要守恒时未禁用
如果系统不守恒某个量子数,必须显式禁用:
```python
BasisMultiElectron([0, 1], sigmaqn=[0, 0])  # 禁用
Op(..., qn=[0, 0])  # 禁用
```
否则结果错误。

## 陷阱 2: 自旋链量子数
Heisenberg 链用 BasisHalfSpin,量子数是自旋投影:
- qntot=0: 总自旋投影为 0
- sigmaqn 决定每个位点的量子数

## 陷阱 3: Hubbard 量子数
Hubbard 模型有两个守恒量: n_up 和 n_down
- sigmaqn=[[0,0],[1,0]] 和 [[0,0],[0,1]]
- qntot=[n_up, n_down]
