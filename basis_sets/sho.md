# BasisSHO — 简谐振子基组

## 构造
```python
from renormalizer import BasisSHO
b = BasisSHO(dof, omega, nbas, x0=0.0, dvr=False, scale_omega=False)
```
- dof: 自由度标识
- omega: 频率 (浮点数, 原子单位 a.u., 不是 Quantity 对象)
- nbas: 截断能级数 (最大占据数+1)
- dvr: 是否使用 DVR 表示 (默认 False)
- scale_omega: 是否无量纲化 (默认 False)

## 可用算符符号
| 符号 | 物理含义 |
|------|---------|
| b^\dagger | 产生算符 |
| b | 湮灭算符 |
| b^\dagger b | 占据数算符 |
| b^\dagger+b | b^\dagger + b (位置相关) |
| x | 位置算符 = 1/sqrt(2\omega) \times (b^\dagger + b) |
| x^2 | 位置平方 |
| p | 动量算符 |
| p^2 | 动量平方 |

## 默认基态
- 默认 [1, 0, 0, ...] 表示基态 |0\rangle (n=0 占据)
- dvr=True 时基态不再是 [1,0,0,...]

## 截断建议
- 常见起始: nbas=16
- 自适应估计: max(16 \times c^{2} / \omega^{3}, 4), c 为耦合强度

## 物理场景
- Spin-Boson 模型的浴部分
- Holstein 模型的声子
- 任何谐振子模式
