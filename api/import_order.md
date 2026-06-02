# Import 顺序陷阱

## 规则
import renormalizer 必须在 import numpy 之前。

## 原因
Renormalizer 在导入时设置环境变量 (MKL_NUM_THREADS 等) 来控制线程数。
如果 numpy 先导入,这些环境变量不会生效。

## 正确写法
```python
import renormalizer
import numpy as np
```

## 错误写法
```python
import numpy as np  # 错误!
import renormalizer
```

## 解决方案
在脚本开头用 os.environ 设置:
```python
import os
os.environ["MKL_NUM_THREADS"] = "4"
os.environ["OMP_NUM_THREADS"] = "4"
import numpy as np
```
