# 学习基础知识

本章将向您介绍旋转目标检测的基本概念，以及旋转目标检测的框架MMRotate，并提供了详细教程的链接。

## 什么是旋转目标检测

### 问题定义
受益于通用检测的蓬勃发展，目前绝大多数的旋转检测模型都是基于经典的通用检测器。随着检测任务的发展，
水平框在一些细分领域上已经无法满足研究人员的需求。通过重新定义目标表示形式以及增加回归自由度数量
的操作来实现旋转矩形框、四边形甚至任意形状检测，我们称之为旋转目标检测。如何更加高效地进行高精度
的旋转目标检测已成为当下的研究热点。下面列举一些旋转目标检测已经被应用或者有巨大潜力的领域：
人脸识别、场景文字、遥感影像、自动驾驶、医学图像、机器人抓取等。


### 术语
旋转目标检测与通用目标检测最大的不同就是用旋转框标注来代替水平框标注，它们的定义如下：
- 水平框: 宽沿x轴方向，高沿y轴方向的矩形。通常可以用四个顶点的坐标表示
`(x_i, y_i)`  (i = 1, 2, 3, 4)，也可以用中心点坐标以及宽和高表示
`(x_center, y_center, width, height)`。
- 旋转框: 由水平框绕中心点旋转一个角度`angle`得到，通过添加一个弧度参数得到其旋转框定义法
`(x_center, y_center, width, height, theta)`。其中，`theta = angle * pi / 180`，
单位为`rad`。当旋转的角度为90°的倍数时，旋转框退化为水平框。标注软件导出的旋转框标注通常采用多
边形定义法`(xr_i, yr_i)` (i = 1, 2, 3, 4)，在训练时需要转换为旋转框定义法。

```{note}
在 MMRotate 中，角度参数的单位均为弧度。
```

实际上，旋转框既可以由水平框绕在中心点顺时针旋转得到，也可以由水平框绕在中心点逆时针旋转得到。
旋转方向和坐标系的选择密切相关。图像空间采用右手坐标系`(y，x)`，其中 y 是`上->下`，x 是`左->右`。
此时存在2种相反的旋转方向：

- 顺时针（CW）旋转
```
# 示意图
0-------------------> x (0 rad)
|  A-------------B
|  |             |
|  |     box     h
|  |   angle=0   |
|  D------w------C
v
y (pi/2 rad)

# 旋转矩阵

|cos(theta) -sin(theta)|
|sin(theta)  cos(theta)|

# 旋转变换

xr_i = -sin(theta) * (y_i - y_c) + cos(theta) * (x_i - x_c) + x_c
yr_i = cos(theta) * (y_i - y_c) + sin(theta) * (x_i - x_c) + y_c
```

- 逆时针（CCW）旋转
```
# 示意图  
0-------------------> x (0 rad)
|  A-------------B
|  |             |
|  |     box     h
|  |   angle=0   |
|  D------w------C
v
y (-pi/2 rad)

# 旋转矩阵
| cos(theta) sin(theta)|
|-sin(theta) cos(theta)|

# 旋转变换
xr_i = sin(theta) * (y_i - y_c) + cos(theta) * (x_i - x_c) + x_c
yr_i = cos(theta) * (y_i - y_c) - sin(theta) * (x_i - x_c) + y_c
```
```{note}
在MMRotate中均使用顺时针（CW）旋转。在MMCV中可以设置旋转方向的算子有：box_iou_rotated (默认为`CW`)，nms_rotated (默认为`CW`)，
RoIAlignRotated (默认为`CCW`)，RiRoIAlignRotated (默认为`CCW`)。*
```

### 旋转框定义法
由于 `theta` 定义范围的不同，在旋转目标检测中逐渐衍生出如下3种不同的旋转框定义法：
- ***D<sub>oc</sub>*** : OpenCV 定义法，`theta∈[-pi / 2, 0)`，与 x 正半轴成锐角的矩形边为 w。
该定义法源于OpenCV中的`cv2.minAreaRect`函数，其返回值为`[-90, 0)`。需要注意的是，从4.5.1版本开始，
`cv2.minAreaRect`的返回值变为了`(0, 90]`（[参考资料](https://github.com/opencv/opencv/issues/19749)）。
为了保持统一，需要将新版角度先转换为老版本角度，再转为弧度。
- ***D<sub>le135</sub>*** : 长边135°定义法，`theta∈[-pi / 4, 3 * pi / 4)` 并且 `w > h`。  
- ***D<sub>le90</sub>*** : 长边90°定义法，`theta∈[-pi / 2, pi / 2)` 并且 `w > h`。


### 评估
评估 mAP 的代码中涉及 IoU 的计算，可以直接计算旋转框 IoU，也可以将旋转框转换为多边形，然后
计算多边形 IoU (DOTA在线评估使用的是计算多边形 IoU)。


## 什么是 MMRotate

MMRotate 是一个为旋转目标检测方法提供统一训练和评估框架的工具箱，以下是其整体框架：:

<div align=center>
<img src="https://raw.githubusercontent.com/zytx121/image-host/main/imgs/mmrotate-arch.png" width=80%/>
</div>

MMRotate 包括四个部分, `datasets`, `models`, `core` and `apis`.

- `datasets` 用于数据加载和数据增强。 在这部分,我们支持了各种旋转目标检测数据集和数据增强预处理。

- `models` 包括模型和损失函数。

- `core` 为模型训练和评估提供工具以及角度转换函数。

- `apis` 为模型训练、测试和推理提供高级API。

## 如何使用教程

下面是 MMRotate 详细的分步指南:

1. 关于安装说明, 请参阅 [安装](install.md).

2. [开始](get_started.md) 介绍了 MMRotate 的基本用法.

3. 如果想要更加深入了解 MMRotate，请参阅以下教程:


- [配置](tutorials/customize_config.md)
- [自定义数据集](tutorials/customize_dataset.md)
- [自定义模型](tutorials/customize_models.md)
- [自定义运行时](tutorials/customize_runtime.md)
