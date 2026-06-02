# 非谐振子基组

## 问题背景
谐振子 (BasisSHO) 假设势能面是抛物线:
V(x) = 1/2\omega^{2}x^{2}

但真实分子的势能面是非谐的, 特别是在:
- 高振动能级 (v > 3-5)
- 解离极限附近
- 内转换过程中 (所有电子激发能注入振动)

## 可用基组

### BasisSineDVR (正弦离散变量表示)
```python
from renormalizer import BasisSineDVR
b = BasisSineDVR(dof, nbas, xi, xf)
```
- dof: 自由度标识
- nbas: 网格点数
- xi, xf: 网格范围 (位置坐标)

**优点**: 动能算符精确 (FFT), 可处理任意势能面
**缺点**: 需要预知波函数的空间范围

### BasisSHO + 非谐修正
在谐振子基中展开非谐势:
V(x) = 1/2\omega^{2}x^{2} + \alphax^{3} + \betax^{4} + ...

通过 Op("x^3", dof) 等算符添加非谐项。

### Morse 振子
V(r) = D_e (1 - e^{-a(r-r_e)})^{2}

可用 BasisSHO 展开 Morse 势 (取足够多能级)。

## n-MR 势能面展开
多参考势能面 (n-MR) 将势能面展开为:
V = \Sigma_n c_n (b^\dagger + b)^n

在算符层面:
Op("(b^\dagger+b)^n", dof, c_n)

## 截断建议
- 谐振子: nbas = max(16 \times g^{2}/\omega^{3}, 4)
- 非谐振子: nbas 需增大 20-50%
- DVR: nbas = 32-64 (取决于势能面宽度)

## Tool 映射
- BasisSineDVR: general_model (model_script)
- 非谐修正: dmrg_ground_state (model_script 添加高阶项)
- n-MR 势能面: general_model (model_script)

## 常见陷阱
- BasisSineDVR 的 xi/xf 必须覆盖波函数主要分布区域
- 非谐修正的阶数需要收敛测试
- Morse 势的 BasisSHO 展开需要足够多能级
