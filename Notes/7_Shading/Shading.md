# Shading

## 目录
+ [Blinn-Phong Reflectance Model](#blinn-phong-reflectance-model)
+ [Shading Frequecies](#shading-frequencies)
+ [Graphics Pipeline](#graphics-pipeline)
+ [Texture Mapping](#texture-mapping)
+ [Barycentric Coordinates](#barycentric-coordinates)

## Blinn Phong Reflectance Model
### 光线类型
以下图为例，将光线分为不同类型：
![lighting_type_example](./images/lighting_type_example.png)
+ Specular highlights，镜面反射高光（也被称为镜面光），可以观察图中比较亮的高光部分，这里接收到的光线经过镜面反射，将光线射入摄像机，这种光被称为镜面光
+ Diffuse reflection，漫反射光，物体除高光外，整体表现出一个变化不明显的颜色，这是光线在物体表面发生漫反射，只有一定类型的光辉射入摄像机，这些光线被叫做漫反射光
+ Ambient lighting，环境光，图中杯子的最左侧，可以看到没有光照直射，但是仍然可以看见光，因为这是光线多次反射折射所形成的光，它的来源很复杂，我们把它简化为一种光，叫环境光，一般是被设置为常量

### 基础着色定义
简化一下着色场景，只考虑对抽象出来的着色点进行着色（我们认为这个着色点是一个平面），我们做出一下基本定义：
+ 观察方向 $\vec V$ ，即我们摄像机的Lookat方向
+ 着色点的法向 $\vec n$ ，即平面的法向
+ 光线的入射方向 $\vec I$
+ 这些值只取方向不取大小，因此他们被定义为单位向量
+ 还有一些其他物体表面的属性
    + 颜色color
    + 光滑度shininess
    + $\cdot \cdot \cdot$
![shading_point](./images/shading_point.png)

着色具有局部性，它只考虑当前这个点本身，不关注这个点是否遮挡其他物体

## Shading Frequecies