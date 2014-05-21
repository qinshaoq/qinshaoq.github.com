---

title: Harris检测算子
layout: post
categories: [image processing]
tags: [Harris, 角点检测, image processing]

---

### 概述
Harris检测算子是Harris和Stephens在1988年的"A combined corner and edge detector"文章中提出的一种角点检测算子。
在介绍Harris算子之前，必须先介绍一下Moravec角点检测算子。

### Moravec检测算子
Moravec检测算子其实是一种非常直观也非常简单的角点检测算子。
角点（Corner Point），指的是一幅2维图像上具有比较大的曲率的点，经常存在于各种连接处，纹理急剧变化的表面，相互遮挡的物体边界等。
像局部极大值点，局部极小值点都属于角点的范畴。
根据我们最直观的认识，如果我们要在一幅图像上找出极大/小值点，要怎么做呢？肯定是找这样的点：它的灰度比周围的8个点的灰度值都大/小。
而Moravec算子正是这样一种思路的实现，具体步骤如下：

* 将要判断的点置于一个3×3或5×5的图像块的中心，如下图红色框所示
* 将红色框朝8个方向移动一格，得到蓝色的框
* 将红色框与蓝色框对应位置的像素值相减，并求平方和，得到8个值，每一个值表示中心点在该方向上的像素变化
* 将8个值中最小的作为中心点的像素变化值（因为角点应该在x,y方向上变化都比较大；而边缘点在一个方向大，另一个方向小）
* 找出每一个点的像素变化值，在局部图像中，该值最大的点位角点

![Description of Moravec detector](/media/image/2014-05-20-Moravec.png)

更严格的数学描述如下：
原图片为$I(x,y)$，经过一个$[u,v]$（这里$u,v\in\{-1,0,1\}$）的平移之后的变化为：
\begin{equation}
E(u,v)=\sum_{x,y}\omega(x,y)[I(x+u,y+v)-I(x,y)]^2
\end{equation}
其中$\omega(x,y)$为窗函数，在Moravec算子计算过程中，窗函数为矩形窗。

** Note: ** 此处使用$I(x,y)$表示图片，故$(u,v)$和$(x,y)$的含义与Harris原始论文中刚好相反。在与论文进行对比时请不要迷惑～

从Moravec算子的计算过程中，我们也可以看出它存在的一些问题：
1. 该算子只考虑了在4个间隔为45度方向上的像素变化，而不是在各个方向上的变化；
2. 因为使用的是矩形窗函数，所以该算子很容易受到噪声的干扰；
3. 该算子对于边缘（edge）的处理过于随意，因为它只考虑了几个方向像素变化的最小值。

### Harris算子的改进
针对Moravec算子出现的3个问题，Harris算子对其一一进行了改进，具体措施如下：
1. 计算了任意方向上的像素变化
$I(x+u,y+v)$作全微分可以得到
$$
I(x+u,y+v)=uI_x+vI_y+O(u^2,v^2)
$$
其中$I_x$和$I_y$表示图像在x和y方向上的变化梯度。
$I_x=\dfrac{\partial I}{\partial x}$,$I_y=\dfrac{\partial I}{\partial y}$
具体计算过程为
$$
I_x=\dfrac{\partial I}{\partial x}=I(x,y)\otimes \begin{bmatrix}
-1 & -1 & -1 \\
0 & 0 & 0 \\
1 & 1 & 1
\end{bmatrix}
$$
$$
I_y=\dfrac{\partial I}{\partial y}=I(x,y)\otimes \begin{bmatrix}
-1 & 0 & 1 \\
-1 & 0 & 1 \\
-1 & 0 & 1
\end{bmatrix}
$$
所以，图像在任意方向$[u,v]$上的像素变化为
$$
[I(x+u,y+v)-I(x,y)]^2 \approx [uI_x+vI_y]^2
$$
此时$u,v\in\mathbb{R}$。

代入公式1中可以得到
\begin{equation}
E(u,v)\approx \sum_{x,y}\omega(x,y)[uI_x+vI_y]^2
=\sum_{x,y}\omega(x,y)\left(u^2I^2_x+2uvI_{xy}+v^2I^2_y\right)
=Au^2+2Cuv+Bv^2
\end{equation}
其中，$A=I^2_x\otimes \omega$，$B=I^2_y\otimes \omega$，$C=I_{xy}\otimes \omega$
由此，我们得到了任意方向$[u,v]$平移之后的像素变化公式，

2. Harris算子将窗函数的形式改为使用平滑的高斯窗
二维高斯函数的形式为：
$$
g(\sigma)=\dfrac1{2\pi \sigma^2}e^{-dfrac{x^2+y^2}{2\sigma^2}}
$$
使用高斯窗函数时需要指定窗的大小hsize和$\sigma$的大小，二者一般是相关的，根据高斯函数的性质，$(-3\sigma,3\sigma)$之间的能量占99.99%，所以一般取hsize=$6\sigma$。
在MATLAB中可以使用`fspecial`函数生成一个高斯窗，下面是一个$\sigma=0.5$的3×3高斯窗

```
g = fspecial('gaussian',3,0.5)
g =

    0.0113    0.0838    0.0113
    0.0838    0.6193    0.0838
    0.0113    0.0838    0.0113
```

计算Moravec算子时直接求8个方向的像素变化的最小值作为该点的像素变化值。
而在计算Harris算子时，得到了任意[u,v]方向的像素变化，如何利用任意方向的像素变化合成该点总的像素变化？此处就需要用到高斯窗函数。


平移[1,0]之后使用积分高斯窗（简化形式：$\sigma \approx 0.6649$）计算的例子如下图所示：

![Example of usage of Gaussian window](/media/image/2014-05-20-Gaussian1.png)

3. 将公式2改写为以下形式
$$
E(u,v)=[u,v]M\begin{bmatrix}u \\ v \end{bmatrix}
$$
其中$M=\begin{bmatrix}A & C \\ C & B \end{bmatrix}$


### 参考
[局部特征(2)——Harris角点][1]

[1]: http://blog.csdn.net/jwh_bupt/article/details/7628665
