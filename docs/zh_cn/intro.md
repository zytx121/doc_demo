# 学习基础知识

本章将向您介绍旋转目标检测的基本概念，以及旋转目标检测的框架MMRotate，并提供了详细教程的链接。

## 什么是旋转目标检测

### 问题定义
受益于通用检测的蓬勃发展，目前绝大多数的旋转检测模型都是基于经典的通用检测器。随着检测任务的发展，
水平框在一些细分领域上已经无法满足研究人员的需求。通过重新定义目标表示形式以及增加回归自由度数量
的操作来实现旋转矩形框、四边形甚至任意形状检测，我们称之为旋转目标检测。如何更加高效地进行高精度
的旋转目标检测已成为当下的研究热点。下面列举一些旋转目标检测已经被应用或者有巨大潜力的领域：
- 人脸识别
- 场景文字
- 遥感影像
- 自动驾驶
- 医学图像
- 机器人抓取


### 术语
旋转目标检测与通用目标检测最大的不同就是用旋转框标注来代替水平框标注，它们的定义如下：
- 水平框: 宽沿x轴方向，高沿y轴方向的矩形。通常可以用左上角以及右下角两个顶点的坐标表示
`(x_lt, y_lt, x_rb, y_rb)`，也可以用中心点坐标以及宽和高表示
`(x_center, y_center, width, height)`。
- 旋转框: 由水平框绕中心点旋转一个角度`angle`得到，通过添加一个弧度参数来表示
`(x_center, y_center, width, height, theta)`。其中，`theta = angle * np.pi / 180`。
当旋转的角度为90°的倍数时，旋转框退化为水平框。标注软件导出的旋转框标注通常采用多边形定义法
`(xr_1, yr_1, xr_2, yr_2, xr_3, yr_3, xr_4, yr_4)`，在训练时需要转换为旋转框表示法。

图像空间采用右手坐标系`(y，x)`，其中 y 是`上->下`，x 是`左->右`。对于一个旋转框存在2种相反
的旋转方向，即顺时针（CW）和逆时针（CCW）。

- CW 的示意图
```
0-------------------> x (0 rad)
|  A-------------B
|  |             |
|  |     box     h
|  |   angle=0   |
|  D------w------C
v
y (pi/2 rad)
```
- CW 的旋转矩阵

$$\begin{matrix}
cos(theta)&-sin(theta) \\
sin(theta)&cos(theta) \\
\end{matrix}$$


- CCW 的示意图
```
0-------------------> x (0 rad)
|  A-------------B
|  |             |
|  |     box     h
|  |   angle=0   |
|  D------w------C
v
y (-pi/2 rad)
```



由于角度定义范围的不同，逐渐衍生出如下3种不同的旋转框定义法：
  - ***D<sub>oc</sub>*** : OpenCV 定义法，。
  - ***D<sub>le135</sub>*** : 长边135°定义法。  
  - ***D<sub>le90</sub>*** : 长边90°定义法。

实际上，旋转框既可以由水平框绕在中心点顺时针旋转得到，也可以由水平框绕在中心点逆时针旋转得到。前者
在MMRotate中本文中默认


### 评估
The classes of dataset are split into two group, base classes and novel classes.
The training set contains all the annotations from base classes and a few annotations from novel classes.
The novel classes performance (mAP or AP50) on test set are used for evaluating a few shot detector.



### 旋转目标检测的基本管道
We will introduce a simple baseline for all the few shot learning tasks to further illustrate how few shot learning work.
The most obvious pipeline is fine-tuning.
It usually consists of two steps: train a model on a large scale dataset and then fine-tune on few shot data.
For image classification, we first pretrain a model with training set using cross-entropy loss, and then
we can transfer the backbone and fine tune a new classification head.
For detection, we can first pretrain a faster-rcnn on training set, and
then fine tune a new bbox head on a few instances to detect the novel class.
In many cases, the fine-tuning is a simple but effective strategy for few shot learning.

## 什么是 MMRotate

MMRotate is the first toolbox that provides a framework for unified implementation and evaluation of few shot classification and detection methods,
and below is its whole framework:

<div align=center>
<img src="https://raw.githubusercontent.com/zytx121/image-host/main/imgs/mmrotate-arch.png" width=80%/>
</div>

MMRotate consists of 4 main parts, `datasets`, `models`, `core` and `apis`.

- `datasets` is for data loading and data augmentation. In this part,
we support various datasets for classification and detection algorithms,
useful data augmentation transforms in `pipelines` for pre-processing image
and flexible data sampling in `datasetswrappers`.

- `models` contains models and loss functions.

- `core` provides evaluation tools and customized hooks for model training and evaluation.

- `apis` provides high-level APIs for models training, testing, and inference.

## 如何使用教程

Here is a detailed step-by-step guide to learn more about MMRotate:

1. For installation instructions, please see [install](install.md).

2. [get_started](get_started.md) is for the basic usage of MMRotate.

3. Refer to the below tutorials to dive deeper:

- Few Shot Classification
    - [Overview](classification/overview.md)
    - [Config](classification/customize_config.md)
    - [Customize Dataset](classification/customize_dataset.md)
    - [Customize Model](classification/customize_models.md)
    - [Customize Runtime](classification/customize_runtime.md)

- Few Shot Detection
    - [Overview](detection/overview.md)
    - [Config](detection/customize_config.md)
    - [Customize Dataset](detection/customize_dataset.md)
    - [Customize Model](detection/customize_models.md)
    - [Customize Runtime](detection/customize_runtime.md)
