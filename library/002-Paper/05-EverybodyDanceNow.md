# Everybody Dance Now
>keywords: GAN, keypoints, openpose, pix2pixHD  
>创建时间：2018-11-11

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## Source
Website: [EverybodyDanceNow](https://carolineec.github.io/everybody_dance_now/)  
Github(unofficial): [pytorch-EverybodyDanceNow](https://github.com/nyoki-mtl/pytorch-EverybodyDanceNow)  

代码来源于东京大学，整个架构很清晰，但还未复现OpenPose中手部、面部关节点的生成，以及论文中的 FaceGAN、 Temporal smoothing等，因此清晰度上还是不足。

---

## <span id="MyPoint">MyPoint</span>

这篇文章实现的是 video to video 的转换，给定一个单人跳舞的视频（source video），将其姿态转换到另一个人的身上，生成另一人跳同样舞蹈的视频（target video）。   
关注这篇文章是觉得这个 Task 有趣，架构上主要用到了 **GAN**, **OpenPose**, 以及 **Pix2pixHD**。   

---

## Method Overview

<img src="https://i.imgur.com/QAy0yL9.png" style="zoom:60%" />

Given a video of a source person and another of a target person, the goal is to generate a new video of the target person enacting the same motions as the source.

[MyPoint](#MyPoint)

## Global Pose Normalization

<img src="https://i.imgur.com/8tkfE7m.png" style="zoom:60%" />

由于两个视频中，人像的位置、大小存在一定的差异，在转换的过程中会带来额外的误差，因此，提出了这个方法将 Pose Keypoints 做一次整体的移动。

论文中第四部分以及 Appendix 中说明了这个方法，但我没有看懂，也没有代码可参考。 留待解决！

## <span id="OpenPose">OpenPose</span>

Github: **[OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose)**

OpenPose 是 MCU 的一个开源项目，实现的是包括面部，肢体，手部共 135个关节点（最多）的预测，详细的可看其 Github。

本文复现的代码使用的是其较为简单的版本，只关注了肢体的 Keypoints-Skeleton. (原文应该是包括了面部以及手部关节点预测的)  
**相关论文：** [Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields](http://openaccess.thecvf.com/content_cvpr_2017/papers/Cao_Realtime_Multi-Person_2D_CVPR_2017_paper.pdf)  
**Code:**  [pytorch_Realtime_Multi-Person_Pose_Estimation](https://github.com/tensorboy/pytorch_Realtime_Multi-Person_Pose_Estimation)

此 Notes 暂不关注其具体实现方法，详细笔记之后仔细研究过再总结。 重点关注 Keypoints 的数据形式。 因为 Keypoints-Skeleton 是作为本文的 **Input** 数据使用的。

### Keypoints-Skeleton

<img src="https://i.imgur.com/DnkEsqk.png" style="zoom:100%" />

图片是骨架图的直观显示，其中包括18个关节点以及17个躯干。

#### OpenPose 输出的形式
OpenPose 输入是一张图片，输出是与原图相同大小的两个矩阵： **pafs** 与 **heatmaps**.

- **heatmaps: [w, h, 19]**  
heatmaps 是人体关节点的热点图，前面18层对应着每一个关节点，第19层是前面18层的整合（具体是求和还是平均，或是其他怎么计算的，待Check OpenPose)。  

TODO: 插入heatmap与pafs的顺序图

**注意：** heatmaps 的19层是有固定顺序的，  
```Python
{0,  "nose"},  {1,  "neck"},    {2,  "Rshoulder"}, {3,  "Relbow"},  
{4,  "Rwrist"},{5,  "Lshoulder"},{6,  "Lelbow"},   {7,  "Lwrist"},  
{8,  "Rhip"},  {9,  "Rknee"},   {10, "Rankle"},   {11, "Lhip"},  
{12, "Lknee"}, {13, "Lankle"},  {14, "Reye"},     {15, "Leye"},   
{16, "Rear"},  {17, "Lear"},   {18,  "background"}  
```

部分 heatmaps 展示：  
<img src="https://i.imgur.com/jugo9XY.png" style="zoom:80%" />

问题： 哪一层对应哪个关节点是否是固定的

- **pafs: [w, h, 38]**  
pafs 是关节点之间的连接图， 18个关节点对应着38个连接图。

部分 pafs 展示：  
<img src="https://i.imgur.com/2kRSX1j.png" style="zoom:80%" />

**注意:** 第一行与第二行是相同两个关节点的连接图，也即是说 pafs 中有半数可以说是重复的，相同两个点的两张图数据分布是一致的，但值不同。 为什么需要这样的结果，Check OpenPose。 同时， 有两个连接图是没有用到的（耳朵到肩膀的连接），因为 COCO 的数据集是包含了这两个连接的，这里输出结果时将其删除了。

下图所示的是上图第一列 pafs 的分布。  
<img src="https://i.imgur.com/StlofJ0.png" style="zoom:60%" />

#### 数据处理
之所以说的是 **复现代码的处理**， 是因为原文并没有说明 Keypoints 作为一个输入时的具体形式。 所以一下的笔记均来自对基于代码的检验。

使用 OpenPose 预训练好的方法，输入一张图片，得到向量图 pafs, 以及关节点的heatmaps。 进一步预处理，得到 pose skeleton 的 label。

label: [h, w], 与原图大小相同的矩阵。 标记的躯干是以连接的关节点末端那个数字来标记的，没有标记的为0， 既是说，label是一个范围为 [0, 17] 的 mask， 1-17分别用于表示每一个躯干。


## Pix2PixHD
 
Github: [pix2pixHD](https://github.com/NVIDIA/pix2pixHD)  
Paper: [High-Resolution Image Synthesis and Semantic Manipulation with Conditional GANs](https://arxiv.org/pdf/1711.11585.pdf)

这是NVIDIA的一篇CVPR 2018的论文，基于 Pix2pix 实现的高分辨率的 Image to Image transform。

Pix2pixHD 的主要观点在于使用多尺度的生成器与判别器去生成多尺度的图像，最后得到一个分辨率较高的 fake Image, 主要内容待详细阅读此论文后另外整理笔记。

## Temporal smoothing



## Face GAN


## Data Collection


## Experiments


