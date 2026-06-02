# 单站算符

## 基本格式
```python
Op(symbol, dof, factor=1.0, qn=0)
```

## 示例
```python
Op("Z", "spin", factor=0.5)         # 0.5 × σ_z
Op(r"a^\dagger a", "e0")            # 电子占据数
Op(r"b^\dagger+b", "v0")            # 声子位置
```

## 符号规则
- 多符号乘积用空格分隔: "X X X" 而非 "XXX"
- DOF 可以是 int, str, 或 tuple
- factor 支持 float, complex, 或 Quantity
