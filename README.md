# unet分割肝脏笔记 #
## 前言 ##
+ 本实验采用Unet网络对2维肝脏进行分割，Unet网络介绍可参考：[论文笔记：用于医学图像分割的卷积网络（U-net）](https://zhuanlan.zhihu.com/p/46251798)
## 原来的思路 ##
### 测试一 ###
+ 最开始采用的训练方式是，将所有的数据集混在一起，然后对数据做`equalization`，之后随机挑选训练集，验证集和测试集，对训练集数据进行弹性形变增强，弹性形变增强原理可参考：[论文笔记：图像数据增强之弹性形变（Elastic Distortions）](https://zhuanlan.zhihu.com/p/46833956),此时训练出来的模型作用到测试集上，Dice的精度达到94%，是目前为止几种测试中精度最高的一种。
+ 但是这种训练思路有问题，测试集的有些中间层切片已经在训练集中被训练过了，所以该模型去拟合测试集，效果很好，但是拟合之前从未出现过测试集效果可能会很差，泛化能力有待验证。

### 测试二 ###
+ 本来只有`sliver07`的数据集，后来加入了`chaos`数据集，由于不同的数据集之间灰阶值差距很大，也就是灰度值方位差别很大，所以先对灰度图像做`clip`和`normalize`，使所有的数据集灰阶值在同一区间。
+ 训练集，验证集和测试集的选择是每组数据的png图片作为一个整体单元，`sliver07`的数据集作为训练数据集，`chaos`数据集作为验证集和测试集，训练集依然弹性增强，训练出来的模型拟合测试集，dice精度86%。**精度有待增强。**

### 测试三 ###
+ 数据集的选择和测试二一样，只不过没有对数据集进行`clip`和`normalize`，模型正在训练中，就目前的训练情况来看，可能会优于测试二的结果。

### 测试三 ###
+ 只是设想，还没有正式实验。
+ 思路：在测试三的基础上对所有数据集做`equalization`，之后再进行训练。

## 代码解释 ##
+ unet_vx.py：Unet网络结构代码，包括数据读取；
+ test_unet.py：加载模型测试代码；
+ elastic_transform.py：弹性形变数据增强代码；
+ data_preprocess/dcm2png.py：将dcm格式数据装换成png格式的代码；
+ data_preprocess/img_normalization.py：对数据进行`equalization`的代码；
+ data_preprocess/pre_process.py：对数据进行`clip`和`normalize`的代码；
+ data_preprocess/showimage.py：对肝脏图片可视化的代码。

