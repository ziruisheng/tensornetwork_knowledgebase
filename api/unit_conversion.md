# 单位转换陷阱

## 规则
所有物理量必须用 Quantity 包装,不要裸用数值。

## 正确写法
```python
from renormalizer.utils import Quantity
omega = Quantity(1532, "cm-1")  # 声明单位
```

## 错误写法
```python
omega = 1532  # 错误!不知道是什么单位
```

## 注意
- Quantity 对象需要 .as_au() 转为原子单位
- 不要直接传 Quantity 对象给 BasisSHO 等
