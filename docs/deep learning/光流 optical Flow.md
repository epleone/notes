# 光流 optical Flow


## 原理



## 传统方法



## 深度学习方法


FlowNet2

We use RAFT to extract optical flow in our approach.
Raft: Recurrent all-pairs field transforms for optical flow.



## 数据

目前模型评估普遍利用的是KITTI2012, KITTI2015, Sintel Clean, Sintel Final 4个数据集。


在光流计算性能指标上，5个最重要的指标分别是端点误差(End-Point Error, EPE)、平均端点误差(Average End-Point Error, AEPE)、每秒帧速率(Frame Per Second,FPS)、角度误差(Angular Error,AE)和平均角度误差(Average Angular Error,AAE)。其中EPE为估计光流和真实光流之间的欧氏距离，用来衡量光流估计的准确程度，AE常用于评估角度误差，二者是互补的，AE对小幅度运动误差敏感，EPE对大幅度运动误差敏感。其定义分别为

![图片](flow_estimation.png)

## 可视化


[GitHub - tomrunia/OpticalFlow\_Visualization: Python optical flow visualization following Baker et al. (ICCV 2007) as used by the MPI-Sintel challenge](https://github.com/tomrunia/OpticalFlow_Visualization)

[python - Visualize Optical Flow with color model - Stack Overflow](https://stackoverflow.com/questions/28898346/visualize-optical-flow-with-color-model)



> [!quote]
> 1. [视觉光流计算技术及其应用-腾讯云开发者社区-腾讯云](https://cloud.tencent.cn/developer/article/2321685)
>


