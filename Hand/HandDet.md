
# yolov3
[GitHub - ultralytics/yolov3: YOLOv3 in PyTorch > ONNX > CoreML > TFLite](https://github.com/ultralytics/yolov3)

# yolov5

[GitHub - ultralytics/yolov5: YOLOv5 🚀 in PyTorch > ONNX > CoreML > TFLite](https://github.com/ultralytics/yolov5)

# yolox
[GitHub - ultralytics/ultralytics: NEW - YOLOv8 🚀 in PyTorch > ONNX > OpenVINO > CoreML > TFLite](https://github.com/ultralytics/ultralytics)

# yolox
[GitHub - Megvii-BaseDetection/YOLOX: YOLOX is a high-performance anchor-free YOLO, exceeding yolov3\~v5 with MegEngine, ONNX, TensorRT, ncnn, and OpenVINO supported. Documentation: https://yolox.readthedocs.io/](https://github.com/Megvii-BaseDetection/YOLOX)


[YOLOv8算法（速通版） - 知乎](https://zhuanlan.zhihu.com/p/639837513)
[YOLOPose算法精读和代码详解（附custom data训练代码修改） - 知乎](https://zhuanlan.zhihu.com/p/599857462)
[YOLOv7-Pose尝鲜，基于YOLOv7的关键点模型测评 - 知乎](https://zhuanlan.zhihu.com/p/548268239)
[yolov8-pose：在yolov8上添加人体关键点检测 - 知乎](https://zhuanlan.zhihu.com/p/618454214)


[你一定从未看过如此通俗易懂的YOLO系列(从v1到v5)模型解读 (上) - 知乎](https://zhuanlan.zhihu.com/p/183261974)

[你一定从未看过如此通俗易懂的YOLO系列(从v1到v5)模型解读 (中) - 知乎](https://zhuanlan.zhihu.com/p/183781646)

[你一定从未看过如此通俗易懂的YOLO系列(从v1到v5)模型解读 (下) - 知乎](https://zhuanlan.zhihu.com/p/186014243)

[# 【pytorch】目标检测：YOLO的基本原理与YOLO系列的网络结构](https://zhuanlan.zhihu.com/p/650878036)


# 前言

[如何评价Yolo v3在Yolo v2基础上的改进？ - 知乎](https://www.zhihu.com/question/269944167/answer/2483674370)

**1. YOLO v1 （2015年-开山之作）**

**问题背景**
之前 two-stage 方法如 R-CNN 把检测问题分成两部分，先生成候选区域（region proposal），再用分类器对区域分类，多阶段训练导致不易优化。

**创新点**
把检测当作回归问题，用一个网络输出位置和类别，实现了一个统一，从检测的角度是 one-stage的。

**检测流程**
- 输入一幅多目标图像
- 将图像划分成多个网格
- 通过网络得到每个网格中2个预测框分别的定位信息和[置信度](https://www.zhihu.com/search?q=%E7%BD%AE%E4%BF%A1%E5%BA%A6&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)，然后用高置信度的预测框的类别判断信息作为网格的类别预测信息
- 针对每个类，先将分类置信度低的预测框去除，再通过NMS去除重复预测框
- 输出位置和类别信息

**优点**
- 快。因为回归问题没有复杂的流程（pipeline）。
- 可以基于整幅图像预测（而不是只看部分,与基于[滑动窗口](https://www.zhihu.com/search?q=%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)和区域提议的技术不同），因此它隐式地编码有关类及其外观的上下文信息。因为能看到图像全貌，与 Fast R-CNN 相比，YOLO v1预测背景出错的次数少了一半。
- 学习到物体的通用表示（generalizable representations），[泛化能力](https://www.zhihu.com/search?q=%E6%B3%9B%E5%8C%96%E8%83%BD%E5%8A%9B&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)好。因此，当训练集和测试集类型不同时，YOLO 的表现比和[R-CNN](https://www.zhihu.com/search?q=R-CNN&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)好得多，应用于新领域也很少出现崩溃的情况。

**缺点**
空间限制：一个单元格只能预测一个类别，这种空间约束限制预测的数量；对相互靠的很近的物体，以及很小的群体检测效果不好。

- 尺度限制：网络有两层[全连接层](https://www.zhihu.com/search?q=%E5%85%A8%E8%BF%9E%E6%8E%A5%E5%B1%82&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)，因此在检测时，模型只支持与训练相同的输入分辨率。
- 定位[鲁棒性](https://www.zhihu.com/search?q=%E9%B2%81%E6%A3%92%E6%80%A7&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)不佳：模型根据训练集数据预测边界框，很难将其推广到少见情况下的对象，同一类物体对不常见的长宽比泛化能力弱。
- [损失函数](https://www.zhihu.com/search?q=%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)不佳：定位误差是影响检测效果的主要原因，[定位损失函数](https://www.zhihu.com/search?q=%E5%AE%9A%E4%BD%8D%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)在不同大小物体的处理上，还有待加强，小物体IOU误差敏感。

**2. [YOLO v2](https://www.zhihu.com/search?q=YOLO%20v2&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D) （2017年-加强提升）**

**问题背景**
- [YOLO v1](https://www.zhihu.com/search?q=YOLO%20v1&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D) 检测性能低。
- 当前的检测任务受数据集标签的限制（数据集必须有标签或通过分类赋予标签）。但是，[标记检测图像](https://www.zhihu.com/search?q=%E6%A0%87%E8%AE%B0%E6%A3%80%E6%B5%8B%E5%9B%BE%E5%83%8F&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)比标记分类图像昂贵得多，所以检测数据和分类数据不是一个规模。

**创新点**
- 针对第一个问题，使用一些方法提升 YOLO v1的性能，得到YOLO v2。
- 针对第二个问题，提出了数据集的结合方法，以及联合训练方法，训练YOLO v2后得到的模型叫YOLO9000。

**优化方法**
- **Accuracy**: Batch Normalization, High Resolution Classifier, Convolutional With Anchor Boxes, Dimension Clusters, Direct [location prediction](https://www.zhihu.com/search?q=location%20prediction&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D), Fine-Grained Features, Multi-Scale Training
- **Speed**: 提出一个新网络Darknet-19，比GoogleNet卷积少。

**训练流程**
论文提出了一种联合训练算法，该算法可以在检测和分类数据上训练目标检测器。 利用标记的检测图像来学习精准定位，同时使用分类图像来增加其“词汇量”和健壮性。

- 数据集结合方法：使用了一个多标签模型来组合数据集，并需要层次化的结构。于是把数据集简化为结构树（[hierarchical tree](https://www.zhihu.com/search?q=hierarchical%20tree&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)）。通过改造图，最后得到一个 WordTree，这样每个节点/标签都有自己的概率。
- 联合训练方法：把检测和分类数据混合，训练过程中遇到带标签的检测图像，就基于 YOLOv2 整个损失函数进行反向传播，遇到分类图像，只反向传播网络的[分类损失](https://www.zhihu.com/search?q=%E5%88%86%E7%B1%BB%E6%8D%9F%E5%A4%B1&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)。

**检测流程**
- 输入一幅多目标图像
- 将图像划分成多个网格
- 通过网络得到每个网格中5个锚定的预测框分别的定位信息和置信度和各自独立的类别预测信息
- 针对每个类，先将分类置信度低的预测框去除，再通过NMS去除重复预测框
- 输出位置和类别信息

**3. YOLO v3（2018年-原作引退）**

**问题背景**
- YOLO v3 的提出不是为了解决什么问题，整篇论文其实是技术报告。
- [YOLO v3](https://www.zhihu.com/search?q=YOLO%20v3&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D) 在 YOLO v2 基础上做了一些小改进，核心思想和 YOLO v2、YOLO9000差不多。

**优化方法**
- 边界框预测：定位任务采用anchor box预测边界框的方法，YOLO v3 使用逻辑回归为每个边界框都预测了一个分数 **objectness score**，打分依据是预测框与物体的重叠度。如果某个框的重叠度比其他框都高，它的分数就是1，忽略那些不是最好的框且重叠度大于某一阈值（0.5）的框
- 采取多标签分类
- 多尺度预测
- 使用新网络 Darknet-53 提取特征

**4. YOLO v4（2020年2月-大量改进）**

**问题背景**
- YOLO原作者之前宣布退出[CV界](https://www.zhihu.com/search?q=CV%E7%95%8C&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)，YOLOv4 的作者其实不是前三篇 YOLO 的一作
- YOLOv4 是对 YOLOv3 的一个改进。它的改进方法就是总结了几乎所有的检测技巧，又提出一点儿技巧，然后经过筛选，排列组合，挨个实验（ablation study）哪些方法有效。
- 值得注意的是文章第二部分相关工作，简直就是目标检测的一个简单综述，阅读该部分，你就能了解模型及方法，如果它提到的每个方法你都了解，说明你在这个方向的研究较全面深入。

**优化方法**
- Mish[激活函数](https://www.zhihu.com/search?q=%E6%BF%80%E6%B4%BB%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222483674370%22%7D)优化。
- DropBlock模块。
- CSP模块。
- SSP模块。
- FPN+PAN。
- CIOU
- DIOU NMS
- Mosaic数据增强。

**5. YOLO v5（2020年6月-性能优越）**

**问题背景**
- Ultralytics放出的YOLO v5，完全用Pyotorch实现，并且支持转ONNX以及CoreML等，方便用户在快速部署。
- 现版本的YOLOv5每个图像的推理时间最快0.007秒。
- YOLO v5的名号，是Ultralytics团队自封的，Glenn Jocher是mosaic数据增强方法的创建者。

**优化方法**
- Foucs优化。
- 不同的CSP模块的应用。
- 工程效率优化。

# yolo v1



