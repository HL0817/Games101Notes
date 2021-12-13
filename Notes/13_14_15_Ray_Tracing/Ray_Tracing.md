# Ray Tracing

## 目录

## 为什么要使用光线追踪
光栅化无法处理全局的一些效果
+ 软阴影
+ Glossy reflection（光线的多次反射）
+ 光线的多次弹射到人眼（间接光照）

光栅化是一种快速但结果不精确的方法
光线追踪恰好相反，它的结果非常精确可信但速度很慢

## Basic Ray-Tracing Algorithm

### Light Rays
先做一些关于光线的定义和说明：
+ 光沿直线传播
    + 图形学中假设光线沿直线传播
    + 忽略物理意义上的波粒二象性
+ 光线之间不会发生碰撞
    + 图形学中假设光线不会碰撞，即使发生交叉，也会直接穿过互不影响
    + 忽略物理意义上的波粒二象性
+ 光线一定是从光源出发，最终到达观察者的眼睛
    + reciprocity——光线的可逆性
        从光源出发到达眼睛等价于眼睛出发逆着光的路径可以到达光源

### Ray Casting
+ 从摄像机到成像平面的任意一个像素位置发射一根光线
+ 在光线射中最近的物体后，将击中点与光源进行连接
    如果是这是一条光通路（击中点与光源之间没有其他遮挡），则表示光可以从这条路径到达人的眼睛，然后可以计算光线在这条光路的对颜色值，从而进行着色

这个方法和光栅化可以得到近似的结果，但是它仍然只计算了一次光线，并没有光线追踪核心的多次反射折射的计算

### Recursive(Whitted-Style) Ray Tracing
递归光线追踪，也就是大名鼎鼎的 Whitted-Style 光线追踪

+ 仍然是从摄像机向着成像平面的任意一个像素位置出发，发射一根光线
+ 光线会发生一次反射，部分光线的能量会沿着反射方向继续往前
+ 光线可能会发生一次折射（玻璃等材质的物体），部分光线的能量会在物体内部沿着折射的入射方向打到物体的另一个位置，再次发生折射，部光线的能力离开物体表面沿着出射方向继续往前
+ 收集所有的光线路径上的击中点到光源的连线，如果形成光通路，则表示这个光通路对成像平面的的颜色值有贡献，最后累计所有的贡献值对成像平面的像素进行着色

其中涉及的光线的定义
+ primary ray：摄像机发射的光线
+ secondary rays：反射、折射等光线
+ shadow rays：判定可见性而连接光源的线（不是光线）

### Ray-Surface Intersection
讨论一下光线和物体表面的交点

#### 光线的表达式
光线可以被一个点和一个方向向量定义出来

Ray equation：
$$\begin{matrix} \LARGE \mathbf{r}(t) = \mathbf{o} + t\mathbf{d} & 0 \eqslantless t < \infty \end{matrix}$$
+ $\mathbf{r}$，光线在某一时间的表示
+ $t$，时间 t ，用来描述不同时刻光线的位置变换
+ $\mathbf{o}$，光线的起点
+ $\mathbf{d}$，光线的方向

#### 光线和球的交点
Ray equation：$\begin{matrix}\mathbf{r}(t) = \mathbf{o} + t\mathbf{d},0 \eqslantless t < \infty \end{matrix}$
Sphere equation：$\mathbf{p} : (\mathbf{p} - \mathbf{c})^2 - R^2 = 0$

如何求交？
非常简单，将光线的方程式带入球的方程，解算出时刻 t 即可
$(\mathbf{o} + t\mathbf{d} - \mathbf{c})^2 - R^2 = 0$
其中 $\mathbf{o}, \mathbf{d}, \mathbf{c}, R$ 都是已知的，可以将方程变换成这样的形式 $at^2 + bt + c = 0$
$a = \mathbf{d \cdot d}$
$b = 2(\mathbf{o - c}) \cdot \mathbf{d}$
$c = (\mathbf{o - c}) \cdot (\mathbf{o - c}) - R^2$
将上面这些已知量带入求根公式 $t = \frac {-b \pm \sqrt{b^2 - 4ac}} {2a}$ 中
根据 $t : 0 \eqslantless t < \infty$ 和 $b^2 > 4ac$ 对结果进行约束，就得到想要的交点

#### 光线和隐式表示求交
Ray equation：$\begin{matrix}\mathbf{r}(t) = \mathbf{o} + t\mathbf{d},0 \eqslantless t < \infty \end{matrix}$
General implicit surface equation：$\mathbf{p} : f(\mathbf{p}) = 0$

将光线和球求交的过程推广到这里来，显然可以得到：
$\mathbf{p} : f(\mathbf{o} + t\mathbf{d}) = 0$
我们仍然取**正实数解（t 要大于0，且 t 不为虚数）**，作为最后求交的结果

#### 光线和Mesh求交
我们最关注的是肯定是对Mesh做求交