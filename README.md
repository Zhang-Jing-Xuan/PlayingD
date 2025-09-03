# Faster R-CNN
## 环境配置：
* Python3.6或者3.7
* Pytorch1.6(注意：必须是1.6.0或以上，因为使用官方提供的混合精度训练1.6.0后才支持)
* pycocotools(Linux: pip install pycocotools;   
  Windows:pip install pycocotools-windows(不需要额外安装vs))
* Ubuntu或Centos(不建议Windows)
* 最好使用GPU训练
* 详细环境配置见```requirements.txt```

## 文件结构：
```
* ├── backbone: 特征提取网络，可以根据自己的要求选择
* ├── network_files: Faster R-CNN网络（包括Fast R-CNN以及RPN等模块）
* ├── train_utils: 训练验证相关模块（包括cocotools）
* ├── my_dataset.py: 自定义dataset用于读取VOC数据集
* ├── train_mobilenet.py: 以MobileNetV2做为backbone进行训练
* ├── train_resnet50_fpn.py: 以resnet50+FPN做为backbone进行训练
* ├── train_multi_GPU.py: 针对使用多GPU的用户使用
* ├── predict.py: 简易的预测脚本，使用训练好的权重进行预测测试
* ├── valisation.py: 利用训练好的权重验证/测试数据的COCO指标，并生成record_mAP.txt文件
* └── pascal_voc_classes.json: pascal_voc标签文件
```

## 预训练权重下载地址（下载后放入backbone文件夹中）：
* MobileNetV2 backbone: https://download.pytorch.org/models/mobilenet_v2-b0353104.pth
* ResNet50+FPN backbone: https://download.pytorch.org/models/fasterrcnn_resnet50_fpn_coco-258fb6c6.pth
 
## 数据集，本例程使用的是PASCAL VOC2012数据集
* Pascal VOC2012 train/val数据集下载地址：http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar

## 训练方法
* 确保提前准备好数据集
* 确保提前下载好对应预训练模型权重
* 若要训练mobilenetv2+fasterrcnn，直接使用train_mobilenet.py训练脚本
* 若要训练resnet50+fpn+fasterrcnn，直接使用train_resnet50_fpn.py训练脚本
* 若要使用多GPU训练，使用```python -m torch.distributed.launch --nproc_per_node=8 --use_env train_multi_GPU.py```指令,```nproc_per_node```参数为使用GPU数量

## Faster RCNN框架图
![Faster R-CNN](https://github.com/WZMIAOMIAO/deep-learning-for-image-processing/raw/master/pytorch_object_detection/faster_rcnn/fasterRCNN.png) 

* 将一张图像输入到backbone得到特征图F。
* 对于F上的每个点映射回原图，根据事先给定锚框尺寸和大小生成若干锚框。将这些锚框输入到RPN网络，判断是前景还是背景并对锚框坐标位置作回归参数预测（第一次修正）。
* 经过筛选后，将这些锚框投影到F上获得相应的特征矩阵。
* 将每个特征矩阵通过ROI pooling层缩放到7*7大小的特征图，接着将特征图展平，串联两个全连接层，再并联两个全连接层，一个输出类别信息，另一个输出边界框回归参数信息。（第二次修正）

## 测试结果

### mobilenetv2+fasterrcnn:

![test](https://github.com/Zhang-Jing-Xuan/FasterRCNN/blob/main/test_result.jpg)

### resnet50+fpn+fasterrcnn:

![test](https://github.com/Zhang-Jing-Xuan/FasterRCNN/blob/main/test_result_resnet_fpn.jpg)

## 参考链接
本项目参考：https://github.com/WZMIAOMIAO/deep-learning-for-image-processing/tree/master/pytorch_object_detection/faster_rcnn

Faster RCNN原理：https://b23.tv/sXcBSP

Faster RCNN代码分析：https://b23.tv/HvMiDy

数据集介绍与制作：https://b23.tv/F1kSCK
