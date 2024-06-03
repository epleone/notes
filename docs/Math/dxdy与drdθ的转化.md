# dxdy与drdθ的转化

$$
\begin{align}
 dx =cos⁡θdr−rsin⁡θdθ \\
 dy = sin⁡θdr+rcos⁡θdθ
\end{align}

$$

事实上，按照

$$dxdy 
\newline=(cos⁡θdr−rsin⁡θdθ)(sin⁡θdr+rcos⁡θdθ) 
\newline =sin⁡θcos⁡θdr2+rcos2⁡θdrdθ−rsin2⁡θdθdr−r2sin⁡θcos⁡θdθ2{\rm d}x{\rm d}y 
\newline =(\cos \theta{\rm d}r-r\sin \theta{\rm d}\theta)(\sin\theta{\rm d}r+r\cos \theta{\rm d}\theta) 
\newline =\sin \theta\cos \theta{\rm d}r^2+r\cos^2\theta{\rm d} r {\rm d}\theta-r\sin^2\theta{\rm d} \theta {\rm d}r-r^2\sin \theta\cos \theta{\rm d}\theta^2 
$$

这个算法算出来的 $dxdy$  也并不是毫无意义，它是 ${\rm d}x$  与 ${\rm d}y$  的张量积 ${\rm d}x\otimes{\rm d}y$  （tensor product），只不过张量积并不符合我们的需求。

原因很简单，我们需要的 ${\rm d}x{\rm d}y$  基本含义是两条邻边分别为 ${\rm d }x$  和 ${\rm d}y$  的平行四边形的有向面积。这里注意两个关键点：

1. 我们把 ${\rm d} x$  和 ${\rm d}y$  视作了两个向量，它们是互相垂直的，因此这个平行四边形就是一个矩形，另外，${\rm d} x$ 和 ${\rm d}y$  还构成了平面向量的一组基底（同理， ${\rm d}r$  和 ${\rm d}\theta$  也是；更准确地说是平面上某点处的所有切向量组成的向量空间的一组基底）

2. 有向面积，这意味着 ${\rm d}x{\rm d}y=-{\rm d}y{\rm d}x$  。


而计算有向面积（乃至更高维的有向体积）时，我们使用的乘积应该是楔积（wedge product）${\rm d}x\wedge{\rm d}y$  。

张量积和楔积这两种“乘法”，有相似之处，例如他们都有结合律，但差异也是很明显的：两个向量的楔积满足反交换律 ${\boldsymbol a}\wedge{\boldsymbol b}=-{\boldsymbol b}\wedge{\boldsymbol a}$  ，这等价于幂零性： ${\boldsymbol v}\wedge{\boldsymbol v}=0$ ，后者的意义也非常明显：两邻边重合的平行四边形面积为 0 。但是对于张量积， ${\rm d}x\otimes{\rm d} y$ 和  ${\rm d}y\otimes{\rm d}x$  是线性独立的，我们可以认为它们几乎没有任何关系。

因此我们的正确算法应该是

$$
\begin{align}
{\rm d}x\wedge{\rm d}y 
&=(\cos \theta{\rm d}r-r\sin \theta{\rm d}\theta)\wedge(\sin\theta{\rm d}r+r\cos \theta{\rm d}\theta) \\
&=\sin \theta\cos \theta{\rm d}r\wedge {\rm d}r+r\cos^2\theta{\rm d} r \wedge{\rm d}\theta-r\sin^2\theta{\rm d} \theta \wedge{\rm d}r-r^2\sin \theta\cos \theta{\rm d}\theta\wedge {\rm d}\theta \\
&=0 + r\cos^2\theta{\rm d} r \wedge{\rm d}\theta+r\sin^2\theta{\rm d} r\wedge{\rm d}\theta +0 \\
&=r{\rm d} r\wedge{\rm d}\theta
\end{align}
$$

[https://www.zhihu.com/question/655141296/answer/3489429289](https://www.zhihu.com/question/655141296/answer/3489429289)