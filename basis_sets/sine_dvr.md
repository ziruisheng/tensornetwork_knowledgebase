# BasisSineDVR — 正弦 DVR 基组

## 构造
```python
from renormalizer import BasisSineDVR
b = BasisSineDVR(dof, nbas, xi, xf, endpoint=False)
```
- dof: 自由度标识
- nbas: 基函数数目
- xi, xf: 网格范围
- endpoint: 是否包含端点 (默认 False)

## 可用算符符号
| 符号 | 物理含义 |
|------|---------|
| I | 恒等 |
| x | 位置算符 (对角) |
| x^2 | 位置平方 |
| cos(x) | cos 函数 |
| sin(x) | sin 函数 |
| p | 动量算符 |
| p^2 | 动量平方 |
| dx | 位置差分 |
| dx^2 | 位置差分平方 |

## 特点
- 固定边界条件 (非周期)
- 通常需要 > 16 个基函数
- 适用于复杂核势能面

## 物理场景
- 非谐振动
- 解离模式
- 需要 DVR 表示的核自由度
