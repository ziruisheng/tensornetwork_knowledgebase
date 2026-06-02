# BasisSimpleElectron — 二态电子基组

## 构造
```python
from renormalizer import BasisSimpleElectron
b = BasisSimpleElectron(dof, sigmaqn=None)
```
- dof: 自由度标识
- sigmaqn: 默认 [0,1]

## 可用算符符号
| 符号 | 物理含义 |
|------|---------|
| a^\dagger | 电子产生算符 |
| a | 电子湮灭算符 |
| a^\dagger a | 占据数算符 |

## 默认基态
- [1, 0] = 空穴态 (|0\rangle)
- [0, 1] = 占据态 (|1\rangle)

## 物理场景
- Hubbard 模型的电子位点
- SSH 模型
- Holstein 模型的电子部分
