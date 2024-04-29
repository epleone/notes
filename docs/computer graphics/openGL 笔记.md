# openGL 笔记
## 入门教程
[主页 - LearnOpenGL CN](https://learnopengl-cn.github.io/)

[3D渲染引擎-part2- 深入理解图形API之 opengl & metal & vulkan - 知乎](https://zhuanlan.zhihu.com/p/528801461)


## 骨骼动画

[3d - OpenGL/Glut draw Pyramid - Skeleton Bones - Stack Overflow](https://stackoverflow.com/questions/21654501/opengl-glut-draw-pyramid-skeleton-bones)

[OpenGL骨骼动画教程 - 知乎](https://zhuanlan.zhihu.com/p/565559427)

[骨骼动画-OpenGL两种实现方法（附代码） - 知乎](https://zhuanlan.zhihu.com/p/632215087)

[Live2D动画引擎的图形学原理及实现 - 知乎](https://zhuanlan.zhihu.com/p/383268858)

[Unity3d杂记]骨骼蒙皮动画 - old张的文章 - 知乎
https://zhuanlan.zhihu.com/p/87583171


## MVP矩阵

[opengl - How to create perspective projection matrix, given focal points and camera principal center - Stack Overflow](https://stackoverflow.com/questions/22064084/how-to-create-perspective-projection-matrix-given-focal-points-and-camera-princ)

camera matrix `K=[fx, s, cx; 0, fy, cy; 0, 0, 1]` and image size `[W, H]`

$$
P = \left[ 
\begin{matrix}
2*fx/W & 0 & 0 & 0 \\
2*s/W & 2*fy/H & 0 &0 \\
2*(cx/W)-1 & 2*(cy/H)-1 & (zmax+zmin)/(zmax-zmin) &1\\
0 & 0 & 2*zmax*zmin/(zmin-zmax) & 0
\end{matrix}
\right] 
$$

 `zmin` and `zmax` represent the near and far Z clipping planes. This formulation assumes that the OpenGL world coordinate frame is chosen as follows:

![enter image description here](https://i.stack.imgur.com/gRnzG.png)

The OpenGL camera is assumed to be located at the origin, looking towards positive Z axis, with a down vector collinear and towards the positive Y axis.

if the world coordinate frame is choosen with inverted z axe

![inverted z axe](https://i.stack.imgur.com/Vwnhs.png)

```
GLdouble perspMatrix[16]={2*fx/w,0,0,0,0,2*fy/h,0,0,2*(cx/w)-1,2*(cy/h)-1,-(far+near)/(far-near),-1,0,0,-2*far*near/(far-near),0};
```

$$
P = \left[ 
\begin{matrix}
2*fx/W & 0 & 0 & 0 \\
0 & 2*fy/H & 0 &0 \\
2*(cx/W)-1 & 2*(cy/H)-1 & -(zmax+zmin)/(zmax-zmin) &-1\\
0 & 0 & -2*zmax*zmin/(zmin-zmax) & 0
\end{matrix}
\right] 
$$