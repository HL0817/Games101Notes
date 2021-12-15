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
这个优化方法的核心就是重心坐标，平面上任意一点都可以由重心坐标和三角形的三个顶点表示出来，我们恰好有平面内三角形的三个顶点
我们可以得到这样的式子：$\mathbf{O} + t\mathbf{D} = (1 - b_1 - b_2)\mathbf{P_0} + b_1\mathbf{P_1} + b_2\mathbf{P_2}$
其中的 $\mathbf{O, D, P_0, P_1, P_2}$ 都是向量，我们要求 $t, b_1, b_2$ ，就是求解行列式
可以直接写出这个行列式的解 $\begin{bmatrix} t \\ b_1 \\ b_2 \end{bmatrix} = \frac{1}{\mathbf{S_1 \cdot E_1}}\begin{bmatrix} \mathbf{S_1 \cdot E_2} \\ \mathbf{S_1 \cdot S} \\ \mathbf{S_2 \cdot D} \end{bmatrix}$ ，然后用 $0 \eqslantless t < \infty$ 和 $0 \eqslantless b_1, b_2, b_1 + b_2 \eqslantless 1$ 约束得到求交的结果
行列式解的变量 $\mathbf{E_1, E_2, S, S_1, S_2}$ 可以由已知量简单计算得到：$\begin{matrix}\mathbf{E_1 = P_1 - P_0} \\ \mathbf{E_2 = P_2 - P_0} \\ \mathbf{S = O - P_0} \\ \mathbf{S_1 = D \times E_2} \\ \mathbf{S_2 = S \times E_1}\end{matrix}$

因此，我们可以统计出光线与平面（三角形）求交的计算量：1 div, 27 mul, 17 add

### Accelerating Ray-Surface Intersection
我们现在回顾一下光线在场景中求交的过程（以最常见的 Mesh 表示为例）：
+ 对场景中每个 mesh 的每个三角形面求交
+ 选择交点的 t 最小的那个

这样有什么问题呢？
每个像素都发射一根光线，并和场景中的所有三角形面求交，并且每根光线还有多次反射折射，计算量太大，导致我们实际进行光线追踪的时候非常慢

举几个比较出名的栗子
+ San Miguel Scene ，场景中有 10.7M 三角形
+ Plant Ecosystem ，场景中由 20M 个三角形

因此，我们需要减少计算次数，来加速做光线追踪的过程，比如现在被广泛使用的包围盒（Bounding Volumes/Boxes）

#### Bounding Volumes
包围盒，Bounding Volume/Box，用一个简单规则的盒子将复杂的对象（边界情况复杂、三角形面数多等等）给包起来，以光线对盒子求交来代替对复杂对象求交
+ 包围盒是完全将对象给包围起来的
+ **如果光线和包围盒没有交点，那么光线一定和包围盒内的物体没有交点**
+ 我们实际使用时，先对包围盒求交，如果有交点再对包围盒对象的三角面求交

#### Ray-Intersection With Box
之所以要将这个方法叫做包围盒，是因为我们最常用的用来包裹物体的对象就是长方体，也就是一般意义上的盒子

所以，我们先来认识一下 Box ，包围盒由 3 对互相平行的面形成的交集
Picture（使用视频里的截图内容比较好，分别截取三个平行面的交集）

这个东西也就是图形学中非常出名的 Axis-Aligned Bounding Box（AABB） ，轴对齐包围盒，包围盒的边和坐标轴 $x, y, z$ 是对齐的

#### Ray-Intersection With Axis-Aligned Box
我们还是从 2D 开始，理解光线和 AABB 求交
我们有包围盒外的任意一根光线 $\mathbf{o} + t\mathbf{d}$ 和一个 2D 的包围盒 $x_0, x_1, y_0, y_1$
+ 先对 $x = x_0$ 和 $x = x_1$ 形成的集合求交点，得到 $t_{min}$ 时刻光线和 $x = x_0$ 相交， $t_{max}$ 时刻光线和 $x = x_1$ 相交
+ 再对 $y = y_0$ 和 $y = y_1$ 形成的集合求交点，得到 $t_{min}$ 时刻光线和 $y = y_0$ 相交， $t_{max}$ 时刻光线和 $y = y_1$ 相交
    + 图中可以看到，$t_{min} < 0$ ，我们先假设光线是一根直线，最后可以在取约束结果的时候将这种情况给处理掉
+ 最后我们对 x 平面交点的连线和 y 平面交点的连线取交集，就得到了光线和包围盒求交的结果

3D 包围盒是由 3 对无穷大的面取交集形成，每对面相互平行并且对面和其他对面两两垂直
将刚才的 2D 过程推广到 3D
+ 光线进入全部 3 个对面内，表示光线进入了包围盒
+ 光线离开任意一个对面，表示光线离开了包围盒

因此对 3D 的包围盒求交：
+ 分别对 3 对平行面求 $t_{min}$ 和 $t_{max}$
    + 即使结果为负也没有关系，可以在约束的时候处理掉
+ 那么进出包围盒的时刻 $t_{enter} = max(t_{min})$ ， $t_{exit} = min(t_{max})$ ，这很好理解
    + 光线最晚进入第三对平行面的时刻，才能保证此时光线进入了所有的平行面
    + 光线最早离开第一对平行面的时刻，意味着光线不满足在所有的平行面内了，必然离开了某一平面
+ 如果 $t_{enter} < t_{exit}$ 说明光线在这段时间内在包围盒中，也就意味着光线和包围盒必然相交

最后我们来解决刚才遗留的问题，光线是射线而不是直线，$t$ 不能小于 0 ，我们来考虑 $t_{enter}$ 和 $t_{exit}$ 的特殊取值情况
+ 如果 $t_{exit} < 0$ ，包围盒一定在光线的背后，那么光线和包围盒不想交
+ 如果 $t_{exit} \eqslantgtr 0$ 并且 $t_{enter} < 0$ ，光线的原点在包围盒内部，那么光线和包围盒有交点

那么总结出光线和 AABB 相交的条件—— $t_{enter} < t_{exit}$ $and$ $t_{exit} \eqslantgtr 0$

#### 为什么要使用AABB
对于一般的平面求交：$\Large t = \frac{\mathbf{p' - o} \cdot N}{\mathbf{d \cdot N}}$

对于轴对齐包围盒求交（以 x 轴为例）：$\Large t = \frac{\mathbf{p'_x - o_x}}{\mathbf{d_x}}$
相当于将光源的方向分解到了 $x, y, z$ 三个轴上，不用再去加入 $\mathbf{N}$ 的计算