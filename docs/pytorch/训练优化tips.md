# 训练优化tips

## EMA

**EMA(Exponential Moving Average)** 即指数滑动平均，与普通的算术平均的不同之处在于EMA会对时间上**距离近的数据赋予更高的权重**，是一种**近似**求平均的方法。

对于深度学习中使用EMA优化模型参数这种情况而言，EMA可以看作是Temporal Ensembling，在模型学习过程中**融合更多的历史状态**，从而达到更好的优化效果。


[EMA的原理及代码实现](https://zhuanlan.zhihu.com/p/657869421)

[pytorch-image-models/timm/utils/model\_ema.py at main · huggingface/pytorch-image-models · GitHub](https://github.com/huggingface/pytorch-image-models/blob/main/timm/utils/model_ema.py)

## 多个loss如何平衡

多个loss引入[pareto优化理论](https://zhida.zhihu.com/search?content_id=449636806&content_type=Answer&match_order=1&q=pareto%E4%BC%98%E5%8C%96%E7%90%86%E8%AE%BA&zhida_source=entity)，基本都可以涨点的。

例子：[Multi-Task Learning as Multi-Objective Optimization](https://link.zhihu.com/?target=https%3A//proceedings.neurips.cc/paper/2018/hash/432aca3a1e345e339f35a30c8f65edce-Abstract.html)  
[github 代码](https://github.com/isl-org/MultiObjectiveOptimization/tree/master)

可以写一个通用的class用来优化一个多loss的[损失函数](https://zhida.zhihu.com/search?content_id=449636806&content_type=Answer&match_order=1&q=%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0&zhida_source=entity)，套进任何方法里都基本会涨点。反正我们在自己的研究中直接用是可以涨的。


我参考的这个库改的~[https://http://github.com/median-research-group/LibMTL](https://link.zhihu.com/?target=https%3A//github.com/median-research-group/LibMTL) 集成了很多loss weighting的策略！

  
  [来源: 深度学习的多个_loss_如何平衡？](https://www.zhihu.com/question/375794498/answer/2307552166)


深度学习的多个loss如何平衡？ - 朱翔宇的回答 - 知乎
https://www.zhihu.com/question/375794498/answer/2363017445
  