# 题目

- **基于自监督学习的图像篡改取证**
  - 针对数字图像篡改<u>检测与定位</u>问题
  - 研究<u>设计和实现</u>一种基于自监督学习的取证<u>算法</u>
  - 完成系统的<u>仿真评测</u>，包括<u>算法性能比较</u>实验
- 思路
  - 研究背景
    - 人工智能的背景
      - 自编码器
      - 支持向量机SVM
    - 噪声处理
    - 图像分割
    - 特征处理
    - 相似度检测
  - 研究意义
    - 模糊图像取证难，去除图像的噪声干扰：监控图像效果不佳的情况，很多细节模糊不易识别分辨，电子数据取证难以获得有价值的线索和证据，需要图像处理工具来对不易分辨的图像进行处理，从而获取最为清晰的影像资料，提高视频监控侦查工作的效率，最大限度还原图像画质，挖掘出图像中有用的信息
    - 以具体情形而定？在消除噪声的同时，还需要不要让图片变得过于平滑，即保留图像中的精细细节和结构
  - 国内外现状
  - 【我的】研究目的
  - 研究思路
    - 算法
      - 对象：grayscale & color noisy images → real-world noisy images & synthetic noise
      - 定性和定量
      - 评估指标：PSNR（Peak Signal-to-Noise Ratio峰值信噪比），MSE（均方误差）
    - 软硬件条件
    - training datasets & test datasets
  - 参考文献

# 基本概念

## 图像

- 对客观存在的物体某种属性的描述
- 图像分类：图片、光图像、数学函数、非可见物理图等

## 数字图像

- 一个被采样和量化后的二维函数f(x,y)

- 每一个元素（像素）具有特定的位置和幅值，例如物理图想的某个点对应数字图像上的某个坐标，该坐标的数值就是这个像素点的颜色或者是灰度值

  ![数字图像](img\physical-and-gray.png)

## 数字图像的获取

### 传感器采集数据

通过相机上的传感器阵列才图像进行采集，模拟人眼成像的过程：光线经过物体的反射到达人眼，产生视觉反应

![用传感器阵列成像、采集数据](img\collect.png)

- 场景元素反映来自照射源的能量
- 成像系统首先收集入射的能力，聚焦在图像平面上
- 传感器阵列产生输出，该输出与每个传感器接收的光的总量成正比
- 后端电路扫描这些输出，转化为模拟信号
- 由成像系统的其他部分数字化，最后输出数字图像

### 图像采样和量化：

将模拟图像经过离散化之后得到数字表示的图像

#### 采样

- 在空间上隔一段距离对数据进行采集
- 在特定区域取样本点
- 把空域上或时域上连续的图像（模拟图像）转换成离散采样点（像素）集合（数字图像）的操作
- “坐标上的离散化”

#### 量化

- 对不同像素点的灰度进行离散化，用数字来表示，例如将灰度分为256个等级
- 把像素的灰度（浓淡）变换成离散的整数值的操作
- **量化所达到的精度取决于<u>采样信号的噪声</u>**
- “幅值上的离散化”

## 像素

## 数字图像处理技术

## 灰度分辨率

指在灰度级别中可分辨的最小变化

## 空间分辨率

图像中可分辨的最小细节，单位一般为： 米/像素

# 噪声

> 噪声是当前众多图像篡改取证方法的**基础**，可深入理解分析

感光度，又称为ISO值，是衡量底片对于光的灵敏程度，由敏感度测量学及测量数个数值来决定，最近已经被国际标准化组织标准化

## 数码相机的噪声类型

> 参考资料：https://zhuanlan.zhihu.com/p/23868867

【特点】：【噪声来源】+【表现】+【对画面的影响】

- 非均匀响应噪声
  - 这种噪声是与亮度成正比
  - 在信噪比曲线上，曲线是一条水平线
- 随机噪声
  - 这种噪声是与亮度的平方根成正比
  - 在信噪比曲线上是一条斜率为 10dB/dec 的直线
- 固定噪声
  - 这类噪声是不随亮度变化
  - 在信噪比曲线上是一条斜率为 20dB/dec 的直线

## 去噪算法 Image Denoising

可调研最新针对真实照片图像的图像去噪算法，是不是更有利于提前图像中的指纹信息

### Recent trends in Denoising

- Two notable denoising algorithms: NLM & BM3D
- 由于卷积神经网络CNN的普及，DnCNN & IrCNN

### 深度学习在图像去噪领域

盲降噪&非盲降噪

提高去噪速度

不同类型的噪声：加性白噪声（高斯噪声）、盲噪声、真实噪声、混合噪声

思路：特征提取 → → 残差学习

#### 基础框架 fundamental frameworks

- Machine learning methods for image denoising 用于图像去噪的机器学习
- Neural networks for image denoising 用于图像去噪的神经网络
- CNNs for image denoising 

#### 深度学习技术 deep learning techniques

略

评估：

- 定量
  - PSNR
  - 图像去噪的运行时间
- 定性
  - 使用视觉图形显示恢复的干净图像

设计损失函数

挖掘边缘信息

# 取证 Forensics

## Forensic Similarity

从噪声残差中提取特征

### 参考资料

- “Real Image Denoising with Feature Attention” 
  - https://blog.csdn.net/liujiuxiaoshitou/article/details/106171150 
  - https://blog.csdn.net/weixin_42096202/article/details/103234140
- “Deep Learning on Image Denoising: An Overview”
  - deep learning software
  - challenges
  - https://zhuanlan.zhihu.com/p/107635056?utm_source=qq
- “Forensic Similarity for Digital Images”
  - forensic similarity
- “Media Forensics and DeepFakes: an overview”
- “Noiseprint: a CNN-based camera model fingerprint”
  - 相机指纹
- “On Addressing the Impact of ISO Speed Upon PRNU and Forgery Detection”









