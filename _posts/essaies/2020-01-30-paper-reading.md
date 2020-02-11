---
layout: post
title: Paper Reading
date: 2019-7-13 19:40
comments: true 
external-url:
categories: linux
permalink: /paper_reading
---
<br>

### VOI 论文 

基于滤波的方法：

MSCKF（2007）, VIO很经典的一篇论文

ROVIO, 光度误差/重投影误差

Stereo-MSCKF 开源的MSCKF（2018）

基于优化的VIO：
OKVIS 最早基于优化的VIO系统（开源）
TRO， SVO+预积分，预积分公式非常细致，复现论文Learn-VIO-ORB
VINS-Mono


### 论文阅读 Is Levenberg-Marquardt the Most Efficient Optimization Algorithm for Implementing Bundle Adjustment? 

论文链接 <https://projects.ics.forth.gr/_publications/0201-P0401-lourakis-levenberg.pdf>


非线性最小二乘
--------------

对于一个最小二乘问题,注意$\mathbf{W}$为对称矩阵： 

$$\begin{aligned}
    \mathop{min} \mathbf{F}(x) &= \mathbf{f}^{T}(x)\mathbf{W}\mathbf{f}(x)\\
    \mathbf{f}(x+h) &\simeq \mathbf{f}(x) + \mathbf{J}\mathbf{h}\\
    F(x+h) &= \frac{1}{2}f(x+h)^{T}Wf(x+h) \simeq L(h) \\
    L(h) &= \frac{1}{2}(\mathbf{f}(x) + \mathbf{J}\mathbf{h}) ^ T W (\mathbf{f}(x) + \mathbf{J}\mathbf{h})\\     &= \frac{1}{2} \left[  f^T(x)Wf(x)  + 2h^TJ^TW(x)f(x) + h^TJ^TWJh  \right] \\
     &= \frac{1}{2} \left[  F(x)  + 2h^Tg + h^THh  \right] \\
     -L(0)+L(h) &= h^Tg + \frac{1}{2}h^THh \\
     -L(0)+L(h_{gn}) &= h_{gn}^Tg + \frac{1}{2}h_{gn}^THh_{gn} \\
                    &= \frac{1}{2} h_{gn}^Tg\end{aligned}$$

增益系数 

$$\begin{aligned}
    \rho &= \frac{F(x+h)-F(x)}{L(h)-L(0)}\\
        &= \frac{F(x+h)-F(x)}{h^Tg + \frac{1}{2}h^THh}\end{aligned}$$

$L(h)$对$h$的一阶导和二阶导为： 

$$\begin{aligned}
    L'(x) = g(x) &= \frac{dL(h)}{dh} = J^TWf(x)+ h^TJ^TWJ\\
    H(x) &= \frac{d^2L(h)}{dh^2} = J^TWJ\end{aligned}$$

令一阶导等于0，可以得到正规方程(normal equation)： 

$$J^TWJh = -J^TWf(x)$$

LM(Dampled Gauss-Newton method, Levenberg-Marquardt Method),正规方程变为： 

$$(J^TWJ + \lambda \mathbf{I})h_{lm} = -J^TWf(x)$$

当阻尼系数$\lambda$比较大的时候，上式趋近于梯度下降法，当$\lambda$趋近于0时，上式趋近于高斯牛顿法

参考论文：

```bash
1. The Levenberg-Marquardt method for nonlinear least squares curve-fitting problems
2. Is Levenberg-Marquardt the Most Efficient Optimization Algorithm for
Implementing Bundle Adjustment?
3. METHODS FOR NON-LINEAR LEAST SQUARES PROBLEMS.
其中1介绍了3中LM中的阻尼因子的更新方法，2对比了在SLAM问题中，Dogleg与LM的精度，结果Dogleg稍好于LM，3详细介绍了Dogleg与LM算法。
```

### 3D地表重建主流框架
`1`. Colmap(开源， 需要cuda)<br>
`2`. OpenDroneMap（开源）<br>
`3`. Altizure(OpenDroneMap（开源）)

Steps of 3D reconstruction by ODM(from <https://community.opendronemap.org/t/where-can-i-find-background-information-on-the-concepts-of-odm/665/2>).

```
In short, the steps are:

    1. Images metadata extraction (GPS location, coordinate system)
    2. Structure from motion (from images to camera positions/orientations and sparse point cloud). We use OpenSfM for this.
    3. Multi view stereo (from camera + images to dense point clouds)
    4. Meshing (from points clouds to triangle meshes, we use mostly a Poisson Reconstruction approach but have support for 2.5D meshing as well)
    5. Texturing (from camera + images + meshes to textured meshes)
    6. Georeferencing (from local coordinates + GPS and/or ground control points to real world coordinates)
    7. DSM/DTM (from real world points to elevation models)
    8. Orthophoto generation (from textured meshes in real world coordinates to GeoTIFF)
```

### When doese label smoothing help by Hinton
论证了知识蒸馏+标签平滑是有用的，具体做法就是用硬标签先生成一个老师模型，再控制温度，训练一个基于软标签（label smoothing）的学生模型。因为之前的迁移学习、模型压缩不知道怎么把两种方法结合起来用，这篇paper提供了详尽的实验参考数据。

