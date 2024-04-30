
# timm 使用

timm库中封装了很好用的学习率调度器，可以方便的实现学习率的预热和[余弦](https://www.zhihu.com/search?q=%E4%BD%99%E5%BC%A6&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222408298107%22%7D)退火，对齐简单的使用方法如下图所示：

![](v2-a3809bd2d049d2033ff587d0210a5554_720w.webp)

![](v2-980e39348863238e4141fdce9f3d67a0_720w.webp)

学习率随epoch的变化

可以看到，使用timm库比自己实现或使用[pytorch](https://www.zhihu.com/search?q=pytorch&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222408298107%22%7D)库里的学习率调度，要简单方便很多。

timm库中的[cosine调度器](https://www.zhihu.com/search?q=cosine%E8%B0%83%E5%BA%A6%E5%99%A8&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222408298107%22%7D)还有很多高级参数，这里没有使用有待继续发掘


[神经网络中 warmup 策略为什么有效；有什么理论解释么？ - 知乎](https://www.zhihu.com/question/338066667/answer/2408298107)


