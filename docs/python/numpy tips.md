# numpy tips




## np.where


``` python
import numpy as np

# 交集
numpy.where((a > 5) & (a < 8), 0, 1)

# 并集
numpy.where((a > 5) | (a < 8), 0, 1)

```


## RAW数据

``` python

# 保存Raw
input_data = np.random.rand(1, 256, 256, 3)
# 转换类型很有必要，决定输出的字节大小
input_data = input_data.astype(np.float32)
print(input_data.dtype， input_data.shape)
input_data.tofile("./checkpoint/dummy/hand_dummy_input.RAW")


# 读取raw
data = np.fromfile('data.raw', dtype=np.uint8)
```

## other

``` python

import numpy as np

# 创建棋盘格矩阵
x = np.zeros((10,10))
x[1::2, ::2] = 1
# Out:
array([[0., 0., 0., 0., 0.],
       [1., 0., 1., 0., 1.],
       [0., 0., 0., 0., 0.],
       [1., 0., 1., 0., 1.],
       [0., 0., 0., 0., 0.]])
       
x[::2, 1::2] = 1
# Out:
array([[0., 1., 0., 1., 0.],
       [1., 0., 1., 0., 1.],
       [0., 1., 0., 1., 0.],
       [1., 0., 1., 0., 1.],
       [0., 1., 0., 1., 0.]])

```