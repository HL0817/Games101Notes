# Games101Notes
最近在看闫令琪的Games101课程，记录一下学习笔记和作业的代码

+ 课程[主页](https://sites.cs.ucsb.edu/~lingqi/teaching/games101.html)
    + [视频链接（BIlibili）](https://www.bilibili.com/video/BV1X7411F744?from=search&seid=15862813116742549159)
+ 本文使用数学渲染库：[KaTeK](https://katex.org/)
    + [Supports](https://katex.org/docs/supported.html)
    + 所有涉及数学公式的章节，都有对应的数学公式渲染后图片版内容，无法渲染数学公式时请阅读图片版内容，章节名_md_to_png.png
+ 笔记索引
    + [Introduction to Computer Graphics](./Notes/1_introduction/Introduction_to_Computer_Graphics.md)
    + [向量和线性代数](./Notes/2_向量和线性代数/Introduction_to_Linear_Algebra.md)
    + [Transformation](./Notes/3_Transformation/Transformation.md)
    + [Viewing transformation](./Notes/4_Viewing_transformation/Viewing_transformation.md)
    + [Rasterization](./Notes/5_Rasterization/Rasterization.md)
    + [Antialiasing and Visibility](./Notes/6_Antialiasing_and_Visibility/Antialiasing_and_Visibility.md)
    + [Shading](./Notes/7_Shading/Shading.md)
****
他人[笔记](https://blog.csdn.net/qq_38065509)，作参考

以下为临时草稿文件：
+ [MD基本要素](https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/markdown-basics)
+ 常用数学公式记录
箭头表示向量：$\overrightarrow{a}$
绝对值：$\lVert a \rVert$
式子整体放大+粗体A+行列式：
>$\LARGE{
    { \mathbf{A} }
    = { \begin{pmatrix} x \\ y \end{pmatrix} }
}$

行列式：
>${ \begin{pmatrix} x \\ y \end{pmatrix} }$

式子整体放大+粗体A与A的转置+行列式：
>$\LARGE{
    { \mathbf{A}^\mathrm{T} }
    = { \begin{pmatrix} x , y \end{pmatrix} }
}$

粗体A与A的转置：
>$
    { \mathbf{A} } + { \mathbf{A}^\mathrm{T} }
$

求根${ \sqrt{x^2 + y^2} }$
点乘：$\LARGE{ { \overrightarrow{a} \cdot \overrightarrow{b} } }$
投影：$\overrightarrow{b}_\perp$

叉乘：
>$\LARGE{
    { \lVert a \times b \rVert }
    = { \lVert a \rVert }{ \lVert b \rVert }\sin\theta
}$

矩阵乘法：
>$\LARGE{
    \overrightarrow{a} \times \overrightarrow{b}
    = \mathbf{A}\overrightarrow{b}
    = \begin{pmatrix} 0 & -z_a & y_a \\ z_a & 0 & -x_a \\ -y_a & x_a & 0 \end{pmatrix} \begin{pmatrix} x_b \\ y_b \\ z_b \end{pmatrix}
}$

不等于：
> $M \not = P$