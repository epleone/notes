# pytorch 量化训练

## 前言

**PyTorch对量化的支持目前有如下三种方式：**

- **Post Training Dynamic Quantization，模型训练完毕后的动态量化；**
- **Post Training Static Quantization，模型训练完毕后的静态量化；**
- **QAT（Quantization Aware Training），模型训练中开启量化。**

## 量化流程

### 插入量化节点

在训练过程中，通过在网络中插入假量化节点来模拟量化和反量化的过程。

``` python
import torch
import torch.nn as nn
from torch.quantization import QuantStub, DeQuantStub

class QuantAwareNet(nn.Module):
    def __init__(self):
        super(QuantAwareNet, self).__init__()
        self.quant = QuantStub() # 新插入内容
        self.dequant = DeQuantStub() # 新插入内容
        self.fc1 = nn.Linear(784, 256)
        self.relu = nn.ReLU()
        self.fc2 = nn.Linear(256, 10)
    
    def forward(self, x):
        x = self.quant(x) # 新插入内容
        x = self.fc1(x)
        x = self.relu(x)
        x = self.fc2(x)
        x = self.dequant(x) # 新插入内容
        return x
```

## 量化配置

 在PyTorch中，量化配置（QConfig）指定了模型量化过程中将使用的量化方案和算子。通过使用不同的QConfig，你可以控制如何[量化模型](https://zhida.zhihu.com/search?q=%E9%87%8F%E5%8C%96%E6%A8%A1%E5%9E%8B&zhida_source=entity&is_preview=1)中的权重和激活。

 PyTorch提供了函数获取默认量化配置：`get_default_qat_qconfig` 。这个函数的一个重要参数是后端，通常是 `'fbgemm'` 或 `'qnnpack'`：

- **`'fbgemm'`**: 主要用于服务器和桌面平台上的x86架构，支持INT8量化。
- **`'qnnpack'`**: 适用于移动设备，也支持INT8量化，优化了ARM架构。

```python
from torch.quantization import get_default_qconfig

qconfig = get_default_qconfig('fbgemm')
```

 这个函数会设置一个QConfig，其中包括针对权重和激活的量化方案。对于QAT，权重通常在前向过程中进行伪量化，而激活则在训练时进行动态量化。

当前也可以自定义QConfig， 见参考链接5。

## 完整流程

``` python
import torch
import torch.nn as nn
from torch.quantization import QuantStub, DeQuantStub
import torch.quantization

class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.bn1 = nn.BatchNorm2d(6)
        self.relu1 = nn.ReLU()
        self.quant = QuantStub()
        self.dequant = DeQuantStub()
        
    def forward(self, x):
        x = self.quant(x)
        x = self.relu1(self.bn1(self.conv1(x)))
        x = self.dequant(x)
        return x
        
    # Fuse Conv+BN and Conv+BN+Relu modules prior to quantization
    # This operation does not change the numerics
    def fuse_model(self):
        torch.quantization.fuse_modules(self, ['conv1', 'bn1', 'relu1'], inplace=True)
    
net = Net()
print('===== Before fuse_model:')
print(net)

print('===== After fuse_model:')
net.train()
net.fuse_model()
print(net)


print('===== Setting qconfig:')
# Specify quantization configuration
# Start with simple min/max range estimation and per-tensor quantization of weights
net.qconfig = torch.quantization.get_default_qat_qconfig('fbgemm')
print(net.qconfig)

print('===== After torch.quantization.prepare:')
torch.quantization.prepare_qat(net, inplace=True)
print(net)


# Do your regular training
training_loop(net)
# 训练过程和正常的一样


# 推理测试
print('===== After torch.quantization.convert:')
# inplace参数控制是否改动原模型, 下面两句等价
net = torch.quantization.convert(net, inplace=False)
torch.quantization.convert(net, inplace=True)
print(net)

```

## pytorch量化开源库

比如： 

[GitHub - huggingface/optimum-quanto: A pytorch quantization backend for optimum](https://github.com/huggingface/optimum-quanto)

## 参考

> [!quote] 
> 1. [Quantization 的那些事 | 棒棒生](https://bobondemon.github.io/2020/10/03/Quantization-%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/)
> 2. [Pytorch量化感知训练（训练过程中做量化）](https://zhuanlan.zhihu.com/p/692995614)
> 3. [Quanto: PyTorch 量化工具包](https://zhuanlan.zhihu.com/p/691603386)
> 4. [Pytorch 量化（三） -- QAT (Quantization Aware Training)](https://zhuanlan.zhihu.com/p/599624964)
> 5. [PyTorch的量化](https://zhuanlan.zhihu.com/p/299108528)
> 6. [Introduction to Quantization on PyTorch | PyTorch](https://pytorch.org/blog/introduction-to-quantization-on-pytorch/)
> 7. [Quantization — PyTorch 2.1 documentation](https://pytorch.org/docs/2.1/quantization.html#quantized-model)