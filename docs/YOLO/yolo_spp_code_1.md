## YOLOSPP源码学习-1 总体架构

[github地址](https://github.com/ultralytics/yolov3)

### 1.环境配置

*  python 3.6 或者 3.7
*  pytorch 1.6 以上（需要官方提供的混合精度训练支持）
*  pycocotools

```
Linux : pip install pycocotools
Windows: pip install pycocotools-windows
```

示例配置：
> numpy\==1.17.0
> torchvision\==0.7.0
> opencv_python\==4.3.0.36
> lxml\==4.2.4
> torch\==1.6.0
> scipy\==1.4.1
> pycocotools\==2.0.0
> matplotlib\==3.2.1
> tqdm\==4.42.1
> tensorboard\==2.1.0
> PyYAML\==5.3.1


### 2.文件结构

```
  ├── cfg: 配置文件目录
  │    ├── hyp.yaml: 训练网络的相关超参数
  │    └── yolov3-spp.cfg: yolov3-spp网络结构配置 
  │ 
  ├── data: 存储训练时数据集相关信息缓存
  │    └── pascal_voc_classes.json: pascal voc数据集标签
  │ 
  ├── runs: 保存训练过程中生成的所有tensorboard相关文件(记录损失、mAP等信息)
  ├── utils: 搭建训练网络时使用到的工具
  │     ├── datasets.py: 数据读取以及预处理方法
  │     ├── img_utils.py: 部分图像处理方法
  │     ├── layers.py: 实现的一些基础层结构
  │     ├── parse_config.py: 解析yolov3-spp.cfg文件
  │     ├── torch_utils.py: 使用pytorch实现的一些工具
  │     └── utils.py: 训练网络过程中使用到的一些方法
  │
  ├── train_utils: 训练验证网络时使用到的工具(包括多GPU训练以及使用cocotools)
  ├── weights: 所有相关预训练权重(下面会给出百度云的下载地址)
  ├── model.py: 模型搭建文件
  ├── train.py: 针对单GPU或者CPU的用户使用
  ├── train_multi_GPU.py: 针对使用多GPU的用户使用
  ├── trans_voc2yolo.py: 将voc数据集标注信息(.xml)转为yolo标注格式(.txt)
  ├── calculate_dataset.py: 1)统计训练集和验证集的数据并生成相应.txt文件
  │                         2)创建data.data文件
  │                         3)根据yolov3-spp.cfg结合数据集类别数创建my_yolov3.cfg文件
  └── predict_test.py: 简易的预测脚本，使用训练好的权重进行预测测试
```

### 3.训练数据的准备以及目录结构

* [数据集准备教程](https://b23.tv/F1kSCK)
* [labelimg工具](https://github.com/tzutalin/labelImg) 可以直接生成 yolo 格式的标签文件 `.txt`
* 为方便图像预处理，将自己数据集的图像大小缩放到与 coco 数据集一致
* 测试图象时最好将图像缩放到 32 的整数倍
* 数据目录：
```
├── my_yolo_dataset 自定义数据集根目录
│         ├── train   训练集目录
│         │     ├── images  训练集图像目录
│         │     └── labels  训练集标签目录 
│         └── val    验证集目录
│               ├── images  验证集图像目录
│               └── labels  验证集标签目录 
```

### 4.标注数据集转化为相关准备文件

#### 4.1 将 VOC 格式数据集改为 YOLO 格式数据集的脚本为 `trans_voc2yolo.py`

> 1.将voc数据集标注信息(.xml)转为yolo标注格式(.txt)，并将图像文件复制到相应文件夹
> 2.根据json标签文件，生成对应names标签(my_data_label.names)

在 VOC 官方数据集中，图片信息在 `JPEGImages/` 文件夹中，标注信息（ xml 文件）在 `Annotations/` 文件夹中， txt 目录在文件夹 `ImageSets/Main/` 中

生成的文件在当前文件夹的 `data/my_yolo_dataset` 中，为生成以下的准备文件做输入

==代码注意事项：==

> 1. ==tqdm== 包是用来封装进度条信息的， ==lxml== 包是用来解析 xml 文件的
> 2. 主要修改的地方为 ==voc_root== ，将其修改为自己计算机 VOC 数据集所在路径
> 3. 注意修改路径时候，Linux 统一用 ==/== ,Windows 统一用 ==\\\\== ,如果在当前文件夹下可以直接写，也可以在前面加 ==.\\\\== ,如果在上一层文件夹则为 ==..==

#### 4.2 根据摆放好的数据集信息生成一系列相关准备文件

```
├── data 利用数据集生成的一系列相关准备文件目录
│    ├── my_train_data.txt:  该文件里存储的是所有训练图片的路径地址
│    ├── my_val_data.txt:  该文件里存储的是所有验证图片的路径地址
│    ├── my_data_label.names:  该文件里存储的是所有类别的名称，一个类别对应一行(这里会根据```.json```文件自动生成)
│    └── my_data.data:  该文件里记录的是类别数类别信息、train以及valid对应的txt文件
```

脚本: `calculate_dataset.py`

生成文件：`my_train_data.txt` `my_val_data.txt` `my_data.data`  `新的my_yolov3.cfg`

该脚本的功能：

> 1.统计训练集和验证集的数据并生成相应.txt文件
> 2.创建data.data文件，记录classes个数, train以及val数据集文件(.txt)路径和label.names文件路径
> 3.根据yolov3-spp.cfg创建my_yolov3.cfg文件修改其中的predictor filters以及yolo classes参数(这两个参数是根据类别数改变的)


需要修改的参数：
```py
# 训练集的 labels 目录路径
train_annotation_dir = "./my_yolo_dataset/train/labels"
# 验证集的 labels 目录路径
val_annotation_dir = "./my_yolo_dataset/val/labels"
# 类别信息列举的.names文件（可自己写）
classes_label = "./data/my_data_label.names"
# 原始 yolov3-spp.cfg 网络结构配置文件（需要手动更新预测器卷积核 filter ） 
cfg_path = "./cfg/yolov3-spp.cfg"
```

### 5.预训练权重下载地址(下载后放入 weights 文件夹)

* ```yolov3-spp-ultralytics-416.pt```: 链接: https://pan.baidu.com/s/1cK3USHKxDx-d5dONij52lA  密码: r3vm
* ```yolov3-spp-ultralytics-512.pt```: 链接: https://pan.baidu.com/s/1k5yeTZZNv8Xqf0uBXnUK-g  密码: e3k1
* ```yolov3-spp-ultralytics-608.pt```: 链接: https://pan.baidu.com/s/1GI8BA0wxeWMC0cjrC01G7Q  密码: ma3t
* ```yolov3spp-voc-512.pt``` **(这是我在视频演示训练中得到的权重)**: 链接: https://pan.baidu.com/s/1aFAtaHlge0ieFtQ9nhmj3w  密码: 8ph3













  
