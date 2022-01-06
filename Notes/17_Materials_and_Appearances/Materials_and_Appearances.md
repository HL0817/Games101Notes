# Materials and Appearances
The Appearance of Natural Materials

外观是材质和光线共同作用的结果，研究材质就研究光线如何和材质相互作用

## 目录

Material == BRDF
渲染方程中的 BRDF 项就表示渲染对象的材质

漫反射材质
Diffuse / Lambertian Material
在漫反射材质表面，光线会被均匀的反射到各个方向上

根据这个特点，我们给入射光线做一个假设 —— 光线是从各个方向均匀的照射着色点

那么根据渲染方程（假设物体不发光） $L_o(\omega_o) = \displaystyle \int_{H^2} f_r L_i(\omega_i) \cos\theta_i d\omega_i$

因为光线均匀的从各个方向入射，我们可以把 $L_i(\omega_i)$ 看做一个常数 $L_i$

带入可得 $L_o(\omega_o) = f_r L_i \displaystyle \int_{H^2} \cos\theta_i d\omega_i$

$\displaystyle \int_{H^2} \cos\theta_i d\omega_i$ 对半球上 $\cos \theta$ 的积分，解出来的积分值是 $\pi$

得到简化的式子 $L_o(\omega_o) = \pi f_r L_i$

光线在漫反射材质上从各个方向均匀的入射，会被均匀的反射到各个方向，结合能量守恒可以得到反射的辐照度等于入射的辐照度 $L_o(\omega_o) = L_i$

最终得到 $f_r = \displaystyle \frac {1}{\pi}$ 表示这种材质完全不吸收能量，多少光线射入着色点，就有多少光线被均匀的反射出去

给结果引入一个常量 $\rho \in [0, 1]$ ，来表示不同颜色的漫反射材质 $f_r = \displaystyle \frac {\rho}{\pi}$

Glossy Material

Ideal reflective / refractive Material

Perfect Specular Reflection

Specular Refraction
Snell's Law