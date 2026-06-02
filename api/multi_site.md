# 多站算符

## 格式
```python
Op(symbol_list, dof_list, factor=1.0, qn=0)
```

## 示例
```python
Op("sigma_+ sigma_-", [0, 1])       # 自旋交换
Op("Z Z", [0, 1], factor=0.25)      # Ising 耦合
Op(r"a^\dagger a", [0, 1], factor=-1)  # 电子跳跃
```

## Jordan-Wigner 变换
费米子到自旋的映射:
```python
a_j   = Prod_{l=0}^{j-1}(Z_l) × sigma_+_j
a_j^+ = Prod_{l=0}^{j-1}(Z_l) × sigma_-_j
```

在 Renormalizer 中通过多站算符实现:
```python
Op("Z + Z -", [i, i, i+1, i+2], factor=t, qn=qn)  # JW 编码
```

## 量子数
```python
Op(..., qn=[[1,0],[0,1]])  # 指定每个符号的量子数变化
```
