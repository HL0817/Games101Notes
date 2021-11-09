# Antialiasing and Visibility

## 目录
+ Antialiasing
    + Aliasing
    + Sampleing theory
    + Antialiasing
+ Visibility
    + Z-buffering

## Antialiasing
### Aliasing
我们光栅化三角形的三角形就像这样：
![rasterizing_result](./images/rasterizing_result.png)
似乎有点不对，我们的三角形被像素表示出来就不是一个完整的三角形。像素是一个有大小的方块，被填色表示某些形状的时候，由于大小限制，就表示得有些问题。这个现象被叫做**走样（Aliasing）**，即像素作为采样过程中的最小单位来说过大了，采样频率过小。

这里举出几个常见的采样的例子：
+ Rasterization = Sample 2D Positions
+ Photograph = Sample Image Sensor Plane
+ Video = Sample Time

接下来看一下常见的走样所形成的问题：
+ Jaggies（锯齿），空间中采样
![jaggies_example](./images/jaggies_example.png)
+ Moire（摩尔纹），对图片做降采样（把图片中的奇数列和奇数行给去掉）
![moire_example](./images/moire_example.png)
+ Wagon wheel effect（车轮效应），时间中采样（常见于高速行驶的车辆，人眼观察车轮会有向后转的假象）
![wagon_wheel_effect](./images/wagon_wheel_effect.png)

总结一下这些问题，走样的本质就是**信号的变换太快（高频），采样的频率太慢（采样间隔大）**

这里我们先给出反走样的方法：**采样之前先做模糊**
以光栅化三角形为例：
![rasterization_antialiased_sampling](./images/rasterization_antialiased_sampling.png)
先对三角形做一次模糊（去掉高频的信号），把它变成一个边界模糊的三角形，采样边界时，离三角形边界近的地方像素颜色深一些，远的地方像素颜色浅一些
对于刚才的锯齿问题，我们做完预先模糊后采样的结果如下：
![antialiasing_jaggies_example](./images/antialiasing_jaggies_example.png)
可以看见锯齿明显减少了，但是为什么做完模糊之后采样会减少我们的走样现象？
让我们来研究一下根本原因

### 采样的理论基础
#### 频域（Frequency Domain）
正弦波（Sines）和余弦波（Cosines）
![sines_and_cosines](./images/sines_and_cosines.png)

我们以 $\LARGE \cos 2\pi fx$ 为例
+ 周期（$T$），每个多久重复自己一次，这里 $\LARGE T = 1$，$\LARGE \cos 2\pi x$ 函数每隔1个周期重复自己一次
+ 频率（$f$），周期的倒数，这里 $\LARGE f = \frac 1 T$

![frequency_cosine_example](./images/frequency_cosine_example.png)
我们可以看出，频率越高代表重复自己一次的间隔越短

傅里叶级数展开（Fourier Series Expansion），周期函数可以由 $\sin$ 和 $\cos$ 函数展开式的权重和近似得到
![Fourier_Series_Expansion](./images/Fourier_Series_Expansion.png)
$\LARGE f(x) = \frac A 2 + \frac {2A\cos(t\omega)} {\pi} - \frac {2A\cos(3t\omega)} {3\pi} + \frac {2A\cos(5t\omega)} {5\pi} - \frac {2A\cos(7t\omega)} {7\pi} + \cdot\cdot\cdot$
可以看到图中的周期函数经过傅里叶变换得到的结果，相似程度随着展开次数的增加而提高

傅里叶变换（Fourier Transform），任何一个函数都可以经过变换得到周期函数
$$\begin{split} 
\LARGE F(\omega) = \displaystyle\int_{-1}^{1}f(x)e^{-2\pi i\omega x}dx \\
\\ 
\LARGE f(x) = \displaystyle\int_{-1}^{1}F(\omega)e^{2\pi i\omega x}d\omega \\
\end{split}
\LARGE e^{ix} = \cos x + i \sin x
$$

![Fourier_Transform](./images/Fourier_Transform.png)
**我们可以把转换后的结果定义为频域，也就是：傅里叶变换把信号从时域（spatial domain）转换到频域（frequency domain）**
那么对于任何函数 $f(x)$ 来说
+ 我们通过傅里叶变换把 $f(x)$ 转换为周期函数 $F(\omega)$
+ 我们通过傅里叶级数展开，用正弦和余弦函数去模拟 $F(\omega)$
+ 结合正弦和余弦的频率可知，频率越高得到的结果与原函数越相似

信号可以看做一种函数，那么看下图
![higher_frequencies_with_fixed_sampling](./images/higher_frequencies_with_fixed_sampling.png)
这里展示了在固定的采样频率下，信号的频率越高，采样出来的结果就越差

现在我们从频率的角度理解走样
![aliasing_in_frequency](./images/aliasing_in_frequency.png)
灰色的线条代表采样的频率，我们可以看见，在相同的采样频率下去采样频率差异巨大的两种函数，得到了相同的结果
也就是说，在当前的采样频率下，我们无法区分两种函数表示的信号

#### 滤波（Filtering）
前面我们提到反走样就是在采样之前先做一次模糊，把高频的信号给去掉，这个去掉某些频率信号的过程被叫做滤波。
**滤波就是去掉某些特定频率的信号（Filtering = Getting rid of certain frequency contents）**
现在我们借助前面提到的频域来理解滤波的过程和结果
##### 频域可视化
![visualizing_image_frequency_content](./images/visualizing_image_frequency_content.png)
+ 我们把左边的信号做傅里叶变换，它从时域变到频域
+ 我们把它的频域表示可视化出来得到右边的图案
    这里有几点需要注意：
    + 中心定义为低频区域，周围定位高频区域，也就是说从中心到四周，频率是在变高的
    + 用亮度来表示不同位置（频率）上有多少信息，图里的中心位置比较亮就表示图片大多是信息都是低频信息
    + 水平和竖直的线含义
    我们分析信号的时候会认为信号是周期性的，在处理非周期信号时（例如图片），我们会人为的重复这个段信号（图片水平和竖直重复），但是这段信号的左右边界不相同，在边界连接处信号发生了剧烈变换（同一张图片收尾相连，这个连接的地方有非常明显的差异），在频域中就呈现出明显的高频差异，表现上就是一条水平和竖直的白色线条
##### 高通滤波
现在我们尝试对这个图片进行滤波，即去除频域中某一块区域（某一段频率）的信息
![high_pass_filter](./images/high_pass_filter.png)
+ 我们把低频区域全部去掉，仅保留高频区域，这被称为高通滤波（High-pass filter），如图中右边所示
+ 然后我们对频域的信号做逆傅里叶变换，还原剩余的高频信息，得到了左边的图像