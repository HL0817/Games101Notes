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
我们最关注的是肯定是对Mesh做求交，有这几个作用：
+ 可以用交点做一些渲染工作，例如可见性（遮挡）判断，阴影判断，光线的作用
+ 可以用来判断光源在mesh内还是外

最直接的求交的方法就是使用光线和 Mesh 的每一个三角形面求交
+ 想法很简单，但是很慢（resolution * Mesh triangle counts）
+ 对于每一个三角形面，我们只考虑光线与它有 0 或 1 个交点，即相交或不想交（为了简化，不考虑共面相交的情况）

如何做光线和三角形面求交？我们可以近一步分解问题，将三角形认为是一个平面，那么就可以得到求交的步骤：
+ 光线和平面求交
+ 判断交点在三角形内还是在三角形外

我们对平面做定义：
只用平面上的一个点和平面的法线就可以定义一个平面：平面上任意一点 $\mathbf{p}$ 与给定点 $\mathbf{p'}$ 的连线如果和法线 $\mathbf{N}$ 垂直，那么任意点 $\mathbf{p}$ 就在平面上
Plane Equation：
$$\LARGE \mathbf{p}:(\mathbf{p - p'}) \cdot \mathbf{N}$$
+ $\mathbf{p}$，平面上任意一点
+ $\mathbf{p'}$，平面方程的已知点
+ $\mathbf{N}$，面法线

和前面隐式表示求交的思路一致，光线和平面求交：
Ray equation：$\begin{matrix}\mathbf{r}(t) = \mathbf{o} + t\mathbf{d},0 \eqslantless t < \infty \end{matrix}$
Plane equation：$\mathbf{p}:(\mathbf{p - p'}) \cdot \mathbf{N}$
将 $\mathbf{r}(t)$ 带入平面方程中可得 $(\mathbf{p - p'}) \cdot \mathbf{N} = (\mathbf{o} + t\mathbf{d} - \mathbf{p'}) \cdot \mathbf{N} = 0$
解出 $\Large t = \frac {\mathbf{(p' - o) \cdot N}} {\mathbf{d \cdot N}}$ ，最后用 $0 \eqslantless t < \infty$ 约束结果即可

求交过程可以使用 Möller Trumbore Algorithm 进行优化，直接算出交点
