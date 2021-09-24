# Introduction to Linear Algebra

## 目录
+ 向量
+ 矩阵

## Vectors
### Basic
+ 向量常被写作 $\overrightarrow{a}$ 或者 $\LARGE{a}$
+ 也可以表示为由起始点指向结束点 $\overrightarrow{AB} = B - A$
+ 同时具有方向和大小（方向和长度）
+ 没有起始位置，表示两个点的相对关系
+ 向量的长度表示为 $\lVert \overrightarrow{a} \rVert$
### 单位向量（Unit vector）
+ 长度为1，$\lVert \overrightarrow{a} \rVert = 1$
+ 求某个向量 $\overrightarrow{a}$ 的单位向量：${\hat{a}} = {\overrightarrow{a}} / {\lVert \overrightarrow{a} \rVert}$
+ 我们一般默认单位向量表示方向，这一点被计算机图形学广泛使用
### 向量求和
![vector_addition](./images/vector_addition.jpg)
+ 几何表示：如图，根据平行四边形法则或三角形法则求和
+ 代数表示：坐标值直接相加即可（参照下面的坐标系表示向量）
+ ${\overrightarrow{a}} - {\overrightarrow{b}} = {\overrightarrow{a}} + (-{\overrightarrow{b}})$，相减时可以转换为做加法
### 向量的坐标系表示
![cartesian_coordinates](./images/cartesian_coordinates.jpg)
+ 向量可以用坐标系（常用正交坐标系）表示
+ 表示时默认向量的起始位置在原点

图形学中默认向量为列向量：$\LARGE{ { \mathbf{A} } = { \begin{pmatrix} x \\ y \end{pmatrix} } }$

转置，行列互换：$\LARGE{ { \mathbf{A}^\mathrm{T} } = { \begin{pmatrix} x , y \end{pmatrix} } }$

向量求长度：$\LARGE{ {\lVert \overrightarrow{ \mathbf{A} } \rVert} = { \sqrt{x^2 + y^2} } }$


[MD基本要素](https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/markdown-basics)
[KaTeK](https://katex.org/docs/supported.html)