# VQ-VAE笔记

论文地址：
[[1711.00937] Neural Discrete Representation Learning](https://arxiv.org/abs/1711.00937)

[轻松理解 VQ-VAE：首个提出 codebook 机制的生成模型 - 知乎](https://zhuanlan.zhihu.com/p/633744455)

[VQ-VAE的简明介绍：量子化自编码器 - 科学空间|Scientific Spaces](https://www.spaces.ac.cn/archives/6760)

[简单得令人尴尬的FSQ：“四舍五入”超越了VQ-VAE - 科学空间|Scientific Spaces](https://kexue.fm/archives/9826)

[关于 VQVAE 直观理解 - 知乎](https://zhuanlan.zhihu.com/p/647399156)

pytorch 代码实现：
[GitHub - lucidrains/vector-quantize-pytorch: Vector Quantization, in Pytorch](https://github.com/lucidrains/vector-quantize-pytorch)

tensorflow代码实现：

[GitHub - bojone/FSQ: Keras implement of Finite Scalar Quantization](https://github.com/bojone/FSQ)


## 动机

之前的AE把潜空间编码成了离散的，但这个潜空间的编码点相互之间关系不大，我们没法进行随机采样。也就是是说我编码一个a生成图像A（月牙照片），编码一个b生成图像B（满月照片），那我们取编码a，让编码a朝着b的方向移动一点点，能不能得到一个稍微满一点的月亮呢？不行，很有可能编码出来的东西啥也不是，因为潜空间里的a和b是没啥关系的离散的。所以AE技术基本都是用来抽特征做分类，没法做图像生成。

然后之前提出的VAE，是把潜空间学成了连续分布，这样就可以做随机采样然后生成图像了，a和b之间也可以采样做图像生成了。效果虽然比AE好但又不是特别好，很多采样生成出来的图还是很拉跨。于是就有人想到了，自然图像数据里男女、是否戴帽子这类特征实际上是离散的呀，所以不应该让潜空间向量有那么大的自由度，VAE的做法还是太自由了。

所以为了让潜空间又能采样、又不至于有太大的自由度，VQVAE这篇文章做了件事：把潜空间规范成了一个码表CodeBook，然后让编码器尽量把图编到codebook涵盖的范围里，让解码器也从codebook的有限范围里开始解码到真实图像（这里肯定有很多疑问，比如这个码表咋来的？是提前设置好的吗？（不是）。是训练出来的吗（对）。咋训练的？（下面说））。

码表是随机初始化的。k是超参数，可以自由设置，一般是256到8192，k越大模型能力越强，但是计算量和内存占用也会更到，反之k越小计算量和内存占用越小，但模型能力也会弱一些。


#todo 

如何将视频多帧和vit结合起来使用？

[Video Transformer调研(自用，不全） - 知乎](https://zhuanlan.zhihu.com/p/496599860)
[Transformer 如何用于视频？最新「视频Transformer」2022综述 - 知乎](https://zhuanlan.zhihu.com/p/459946348)
