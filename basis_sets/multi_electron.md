# BasisMultiElectron — 多电子态基组

## 构造
```python
from renormalizer import BasisMultiElectron
b = BasisMultiElectron(states, sigmaqn=None)
```
- states: 电子态列表,如 ["S0", "S1", "S2"]
- sigmaqn: 量子数列表

## 可用算符符号
| 符号 | 物理含义 |
|------|---------|
| a^\dagger | 产生算符 |
| a | 湮灭算符 |
| a^\dagger a | 占据数算符 |

## 初始态指定
- {"S0": 0} \rightarrow S0 态
- {"S1": 1} \rightarrow S1 态
- {("S0","S1","S2"): 1} \rightarrow S1 态

## 物理场景
- FMO 等多能级系统
- 光化学过程中的多电子态
- 需要在多个电子态之间跃迁的系统
