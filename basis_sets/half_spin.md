# BasisHalfSpin — 自旋 1/2 基组

## 构造
```python
from renormalizer import BasisHalfSpin
b = BasisHalfSpin(dof, sigmaqn=None)
```
- dof: 自由度标识 (str 或 int)
- sigmaqn: 两个自旋局域态的量子数; 默认 `None`, 源码会设为 `[0, 0]`
  (禁用该自由度的量子数守恒)
- 构造函数只有 `dof` 和 `sigmaqn` 两个参数。不要使用额外关键字参数来指定
  站点、默认 Pauli 算符或自旋方向; 这些不是 Renormalizer API。

## 可用算符符号
| 符号 | 物理含义 |
|------|---------|
| X / sigma_x | \sigma_x 自旋翻转 |
| Y / sigma_y | \sigma_y |
| Z / sigma_z | \sigma_z 自旋投影 |
| sigma_+ | 自旋升起算符 |
| sigma_- | 自旋降低算符 |
| iY | i \times \sigma_y |
| I | 恒等算符 |

## 常见用法
```python
from renormalizer import BasisHalfSpin, Op

basis = [BasisHalfSpin(i) for i in range(4)]
Op("Z", "spin", factor=h)           # 磁场
Op("sigma_+ sigma_-", [0, 1])       # 自旋交换
Op("X", "spin", factor=Delta)       # 横场
```

## 量子数
- `sigmaqn` 必须是长度为 2 的 list, 分别对应两个局域态; 不要传标量。
- 默认 `sigmaqn=[0,0]`: 量子数守恒被禁用 (两态 qn 相同)
- 自定义 sigmaqn 例: `BasisHalfSpin(dof, sigmaqn=[0,1])` \rightarrow 两个局域态 qn 分别为 0 和 1
- qntot=0: 总自旋投影守恒

## 物理场景
- Heisenberg 自旋链
- Spin-Boson 模型的系统部分
- 任何自旋-1/2 系统
