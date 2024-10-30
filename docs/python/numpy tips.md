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


``` python

from io import BytesIO
from PIL import Image

img = cv2.imread(img_path, cv2.IMREAD_COLOR)

with open(img_path, "rb") as f:
     img_buffer = f.read()
     
img_code = np.frombuffer(img_buffer, np.uint8)
image = cv2.imdecode(img_code, cv2.IMREAD_COLOR)

# 使用 `BytesIO` 将字节流转换为文件对象
img = Image.open(BytesIO(img_buffer))
image = np.array(img)
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