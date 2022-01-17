# Learn the Basics

This chapter introduces you to the basic conception of rotation detection, and the framework of
MMRotate, and provides links to detailed tutorials about MMRotate.

## What is rotation detection

### Problem definition
Few shot learning aims at generalizing to new tasks based on a limited number of samples using prior knowledge.
The prior knowledge usually refers to a large scale training set that has many classes and samples,
while the samples in new tasks are never seen in the training set.
For example, in few shot image classification, a pre-trained model only can see
five bird images (each class has one image and doesn't exist in the pretrained dataset) and
predict the class of bird in the query image.
Another example in few shot detection is that a detector needs to detect the new categories based on a few instances.

In summary, few shot learning focus on two aspects:
- how to embed prior knowledge into models (pre-train with large scale dataset)
- how to transfer knowledge to adapt to new tasks (learn on a few labeled samples).


<div align=center>
<img src="https://raw.githubusercontent.com/zytx121/image-host/main/imgs/difference.png" width=80%/>
</div>

### Terminologies in rotation detection
- Training set: every class in the training set has many samples, and it is big enough for training a deep neural network.
- Support set: a small set of labeled images and all the classes do not exist in the training set.
- Query set: unlabeled images to predict and share the same classes with support set.
- N way K shot: the support set setting, and it means support images contain N classes and
  each class has K samples.
  - For classification, there will be NxK support images in a support set.
  - For detection, there will be NxK support instances in a support set,
    and the number of images can be less than NxK.


### Evaluation
The classes of dataset are split into two group, base classes and novel classes.
The training set contains all the annotations from base classes and a few annotations from novel classes.
The novel classes performance (mAP or AP50) on test set are used for evaluating a few shot detector.



### The basic pipeline for rotation detection
We will introduce a simple baseline for all the few shot learning tasks to further illustrate how few shot learning work.
The most obvious pipeline is fine-tuning.
It usually consists of two steps: train a model on a large scale dataset and then fine-tune on few shot data.
For image classification, we first pretrain a model with training set using cross-entropy loss, and then
we can transfer the backbone and fine tune a new classification head.
For detection, we can first pretrain a faster-rcnn on training set, and
then fine tune a new bbox head on a few instances to detect the novel class.
In many cases, the fine-tuning is a simple but effective strategy for few shot learning.

## What is MMRotate

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

## How to Use this Guide

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
