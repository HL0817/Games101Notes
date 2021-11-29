# Geometry

## 目录
+ 几何的表达方式
+ 曲线
+ 曲面
+ 几何处理

## 几何的表达方式
### 隐式表示和隐式表示
+ Implicit representation of geometry，几何的隐式表示，指将几何体表面的点归纳到特定的关系中
    换句话说，就是用几何表面的点满足的函数来表示这个几何体

    ![implicit_geometry_example](./images/implicit_geometry_example.png)

    如图所示，这样一个几何体可以用类似 `f(x, y, z) = 0` 的函数关系式表示
+ Explicit representation of geometry，几何的显式表示
    + 直接给点的位置
    + 通过参数映射
        + 给定2维的坐标 $(u, v)$ 和2维到3维的映射函数
        + $f:\R^2 \rarr \R^3 \Rightarrow (u, v) \rarr (x, y, z)$

    ![explicit_geometry_example](./images/explicit_geometry_example.png)

### 隐式表示和显式表示的差异
#### 描述几何体
+ 隐式表示非常简洁，但是非常抽象，很难描述几何体具体形状
+ 显式表示很容易将几何体的形状的给描述出来

![descript_a_geometry](./images/descript_a_geometry.png)

例如图中这样形状的几何体：
+ 隐式表示：$f(x, y, z) = (2 - \sqrt{x^2 + y^2})^2 + z^2 - 1$ ，这个式子我们很难去想象出图里的形状
+ 显式表示：2维坐标点，以及映射关系 $f(u, v) = ((2 + \cos u)\cos v, (2 + \cos u)\sin v, \sin u)$ ，我们可以算出每一点的坐标，得到表面坐标就等于知道了这个几何体的形状

同样的形状，几何的显式表示比隐式表示更容易被描述出来

#### 判断点和几何体的位置关系
给定点 $(3/4, 1/2, 1/4)$ ，判断在下面图形内部还是外部

![inside_or_outside_of_geometry](./images/inside_or_outside_of_geometry.png)

+ 隐式表示：$f(x, y, z) = x^2 + y^2 + z^2 - 1$，做法很简单，直接将点带入这个表达式，然后将结果和0做比较
    + 小于0，在几何体内
    + 等于0，在几何体表面
    + 大于0，在几何体外
+ 显式表示：$f(u, v) = (\cos u \sin v, \sin u  \sin v, \cos v)$，我们很难判断给定的点在显式几何体内还是外

同样的形状，几何的隐式表示比显式表示更容易得到给定点的位置关系