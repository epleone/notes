# 训练优化tips

## EMA

**EMA(Exponential Moving Average)** 即指数滑动平均，与普通的算术平均的不同之处在于EMA会对时间上**距离近的数据赋予更高的权重**，是一种**近似**求平均的方法。

对于深度学习中使用EMA优化模型参数这种情况而言，EMA可以看作是Temporal Ensembling，在模型学习过程中**融合更多的历史状态**，从而达到更好的优化效果。


[EMA的原理及代码实现](https://zhuanlan.zhihu.com/p/657869421)

[pytorch-image-models/timm/utils/model\_ema.py at main · huggingface/pytorch-image-models · GitHub](https://github.com/huggingface/pytorch-image-models/blob/main/timm/utils/model_ema.py)


