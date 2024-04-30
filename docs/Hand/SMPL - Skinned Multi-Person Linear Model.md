---
tags: [todo]
---

参考链接：
[虚拟人（SMPL） CVPR 教程专项一：SMPL的介绍、历史与基础 - 知乎](https://zhuanlan.zhihu.com/p/600583930)
[虚拟人（SMPL） CVPR 教程专项二：基于图像优化的SMPL模型 - 知乎](https://zhuanlan.zhihu.com/p/600827310)
[SMPL学习笔记 - 知乎](https://zhuanlan.zhihu.com/p/420090584)
[SMPL源码解读 - 知乎](https://zhuanlan.zhihu.com/p/659631411)
[基于SMPL的三维人体重建-SMPL模型的计算与构建（3） - 知乎](https://zhuanlan.zhihu.com/p/458868557?utm_id=0)
[论文超详细精读：SMPL: A Skinned Multi-Person Linear Model-CSDN博客](https://blog.csdn.net/IanYue/article/details/127206953)



　SMPL（Skinned Multi-Person Linear Model）是一种裸体的、基于顶点的人体三维模型，能够精确地表示人体的不同形状和姿态。
　SMPL模型含有 $N=6890$  个顶点， $F=13776$ 个面片，$K=24$ 个关节点（注：23个关节点+1个根节点），模型参数由$\Phi = \{{T, \mathcal{W}, \mathcal{S}, \mathcal{J},\mathcal{P}\}}$ 定义。
　![[v2-e1f23aacaefd553069840192fc6c47c8_720w.webp]]
　具体来说：
　- $\mathcal{T}$: T-Pose,  平均模型, 平均shape
　- $\mathcal{S}$:  shape的权重矩阵， 将10维的shape 参数$\beta$ 转换为Shape偏移
　- $\mathcal{P}$:  pose的权重矩阵，将pca降维后的pose 参数$\theta$ 转换为Shape偏移 
　- $\mathcal{W}$: LBS的权重矩阵，即计算关节点对Shape的影响，也就是偏移
　- $\mathcal{J}$: 从rest-pose回归出 需要的3D joint点
　- $\beta$: 形状参数，由pca降维到10维
　- $\theta$: 姿态参数，一般和关节数是相等的，直接控制每个关节的旋转，也可能PCA降维。




# SMPL - Skinned Multi-Person Linear Model

参考[基于SMPL的三维人体重建-SMPL模型的计算与构建（3） - 知乎](https://zhuanlan.zhihu.com/p/458868557?utm_id=0) 

**发现很多人问已知3d关节点坐标如何恢复smpl姿态，此时可通过inverse kinematics来求解**

- Kama: 3d keypoint aware body mesh articulation
 - Hybrik: A hybrid analytical-neural inverse kinematics solution for 3d human pose and shape estimation
- Skeleton2mesh: Kinematics prior injected unsupervised human mesh recovery
- [GitHub - nghorbani/human\_body\_prior: VPoser: Variational Human Pose Prior](https://github.com/nghorbani/human_body_prior/tree/master)
- [GitHub - Mathux/ACTOR: Official Pytorch implementation of the paper "Action-Conditioned 3D Human Motion Synthesis with Transformer VAE", ICCV 2021](https://github.com/Mathux/ACTOR)


可以参考hybirk和skeleton2mesh, minimal-ik 
可以看下这里，里面实现了根据keypoint转换到SMPL模型的代码:[GitHub - Mathux/ACTOR: Official Pytorch implementation of the paper "Action-Conditioned 3D Human Motion Synthesis with Transformer VAE", ICCV 2021](https://github.com/Mathux/ACTOR)
