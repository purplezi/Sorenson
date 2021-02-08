![](pic/say_yolo_again.png)

## YOLO学习笔记

### 1.yolo v1

> 论文来源：2016CVPR
> [参考网站](https://pjreddie.com/darknet/yolo/)

#### 1.1 基本思想：

(1)将一幅图像分成 $S \times S$ 个网格(grid cell)，如果某个 object 的中心落在网格之中，则这个网格就负责预测这个 object 。

(2)每个网格预测 **B** 个 bounding box ，每个 bouding box 除了要预测位置 `(x,y,w,h)` 外，还要附带一个 `confidence` 值（ yolo 独有， Faster-RCNN 和 SSD 没有），同时还要预测 **C** 个类别的分数。

> eg.Pascal VOC:Our Fianl Prediction is a 7 \* 7 \* 30 tensor.
> S = 7 ( grid cell 为 7 \* 7 )
> B = 2 ( 2 个 bounding box ,每个包含 x , y , w , h , confidence )
> C = 20 ( classes_num = 20 )
> 7 \* 7 \* (2 \* 5 + 10) -> 7 \* 7 \* 30

![](pic/yolov1_cell.png)

> 每个 bouding box 的坐标是相对值，取值在[0,1]
> x,y : the center of the box **relative to the bounds of the grid cell**
> w,h : the width and height **relative to the whole image**
> confidence : $Pr(Object) \times IOU_{pred}^{truth}$ ,前者为 0 或 1 ，后者为预测框和实际框的交并比


(3)网络结构

 yolov1 的网络结构并不复杂，如下图所示

`其中 s 为步距 stride 的简写，未写的默认为 1 `

![](pic/yolov1_net.png)

最后两层： 4096 -> 1470 ->( reshape 处理) 7  \* 7 \* 30  （即上述的 cell 结构）

#### 1.2 损失函数

> yolov1 的损失函数由三部分组成,所用方法为误差平方和
> (1) bouding box loss
> (2) confidence loss
> (3) classes loss


![](pic/yolov1_lossfuction.png)

==tips:==

(1) bouding box 损失中的宽高损失计算需要**开根号**，目的是**防止不同尺寸的目标框偏移相同距离时的 loss 值一样**（偏移相同的距离，对于大目标而言， IoU 较大，则loss值应该相对较低；对于小目标而言， IoU 较小，则loss值应该相对较高，故采用开根可以起到如此效果）

![](pic/yolov1_boundingboxloss.png)

(2) confidence 损失计算中分为正样本和负样本的计算，根据正负样本真实值 $ \hat{C_i}$ 取 1 或 0 

#### 1.3 局限性

(1) 群体性目标难预测，例如一群飞鸟，不适合密集性对象

(2) 当目标出现新的尺寸或配置时，预测性能较差

(3) 定位不准确是主要问题（缺少 anchor ）

### 2.yolo v2

### 3.yolo v3
