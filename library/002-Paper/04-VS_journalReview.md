# Video Summary 方向 journal 论文相关
>keywords: video, summary, journal  
>创建时间：2018-09-27

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

整理一些关于Video Summary的杂志论文的相关信息，本文不涉及具体的方法理论，主要是论文出处、实验设计、使用的数据集以及相关评估方式的简要概括。

---

## 1. Perceptual Video Summarization

**非CNN方法**

**Title:** Perceptual Video Summarization—A New Framework for Video Summarization

Perceptual： 知觉的，有感知的

```   
@article{thomas2017perceptual,
  title={Perceptual video summarization—A new framework for video summarization},
  author={Thomas, Sinnu Susan and Gupta, Sumana and Subramanian, Venkatesh K},
  journal={IEEE Transactions on Circuits and Systems for Video Technology},
  volume={27},
  number={8},
  pages={1790--1802},
  year={2017},
  publisher={IEEE}
}
```   

本文是基于作者先前的工作<**Perceptual synoptic view of pixel, object and semantic based attributes of video**>, 应用到 VS 方向上的， 与许多仅使用统计冗余的摘要算法不同，首次在 summarization framework 内引入人类视觉系统的特征，以便强调感知重要事件，同时从摘要中消除感知冗余。 从主观和客观两个方面的评估分数评估了此 framework 。  

通俗的来说就是，文章认为一个理想的视频摘要不应只是能说明这个视频的内容信息的关键帧或关键的小段，它应考虑到人类的视觉感知体验，得到的摘要在视觉体验上是合理的且连贯的。

这片文章中，summary 的合成图像开始我以为只是一种表现形式，但看论文应该是直接生成的，这是视频摘要，也是视频关键内容的模式转换，可看与其对比的 Paper: Salient Stills。

看这个文章的 summary 的图像的时候，有一个很自然的想法，就是：如果我只是对视频间隔取帧，是不是也可以做到这一种效果？答案自然不是，一方面，一个1-5min 的视频需要提取关键部分，二是需要对视频的显示效果进行处理。文章基于作者先前的工作很自然的应用到了这个方面。

### 使用的数据集
Dataset: OVP, VSUMM, Vimeo, YouTube  
所有使用视频 1-5min 不等，

### 视频预处理的方式
1. Sampling and Rescaling: 采样为5-10 frames/s  
2. Noise Filtering: 通过计算像素值的标准差，设定阈值，去除视频中的低于某一范围的前后帧（开始与结束）。

### 评估方式
#### 1.直观性比较
直接使用合成图的比较
#### 2.客观比较——结构相似性度量(SSIM)
文章评估summarized frames 和 all frames 之间的结构相似性，但结构相似性计算的是两个图像之间的，我不理解这里的计算方法, 如何将视频（还是两个不同帧的视频）去计算SSIM，文中只说计算的是8*8的两个图像，估计只有看代码能知道了。

参考资料：Blog: [PSNR和SSIM](https://www.cnblogs.com/vincent2012/archive/2012/10/13/2723152.html)

#### 3.时间复杂度的比较
文章的时间复杂度更高，但还是拿来比较了，这精神可真不错。

#### 4.主观性评估
作者找了20个人在5个方面(5个问题)对生成的summary做一个评估（1-5分），得出平均平均分展示，根据来源： [多媒体应用的主观视频质量评定方法-ITU-T Recommendation P.910](https://www.itu.int/rec/T-REC-P.910-200804-I/en)（TODO: 已存，待了解）。

#### 5.关键帧的数量比较
总的来说就是本文的关键帧数量更少。

### 附

- 统计冗余： 从信息论的观点来看，描述信源的数据是信息和数据冗余之和，即：数据=信息+数据冗余。统计冗余是图像数据中经常存在的一种数据冗余。针对统计冗余，通常采用什么技术进行处理呢？最常用的是熵编码压缩技术。熵编码的主要思想是这样的：在一串由许多数值构成的数据中，对那些经常出现的值用短的码组来表示，对不经常出现的值用长的码组来表示，从而最终用于表示这一串数据的总的码位，相对于用固定长度码组来表示的码位而言得到了降低。（来源：[百度百科](https://baike.baidu.com/item/%E7%BB%9F%E8%AE%A1%E5%86%97%E4%BD%99/16329427)）


## 2.A General Framework for Edited Video and Raw Video Summarization


```
@article{li2017general,
  title={A general framework for edited video and raw video summarization},
  author={Li, Xuelong and Zhao, Bin and Lu, Xiaoqiang},
  journal={IEEE Transactions on Image Processing},
  volume={26},
  number={8},
  pages={3652--3664},
  year={2017},
  publisher={IEEE}
}
```  

从摘要看，本文设计了四个模型，来得到视频摘要的相关属性，一是重要的人物和对象（重要性），二是是否代表视频内容（代表性），三是关键镜头是否有类似（多样性），四是故事情节的平滑性（故事情节）。

### 使用的数据集

**Dataset:** TVsum(编辑过的视频), SumMe(短原始视频), ADL(长原始视频)

**Training Set:** 30个video, 15个从TVSum中随机选，15个从Summe中随机选。

### 评估函数
F-measure


## 3.SGSNN

**Title:** Sequence-Guided Siamese Neural Network For Video Summarization of Unmanned Aerial Vehicles

unmanned aerial vehicles (UAVs): 无人机

```
@inproceedings{chen2017sequence,
  title={Sequence-guided siamese neural network for video summarization of unmanned aerial vehicles},
  author={Chen, Jin and Wang, Yi and Chen, Zehan and Zou, Yuexian},
  booktitle={Digital Signal Processing (DSP), 2017 22nd International Conference on},
  pages={1--5},
  year={2017},
  organization={IEEE}
}
```  

翻译部分摘要： 无人机的航拍视频帧几乎只有逐渐变化。在本文中，为了捕捉无人机帧之间的微妙变化，开发了序列引导的连体神经网络（SGSNN）方法来提取语义特征。具体地，顺序相关性已被结合到所提出的SGSNN的学习目标中，其通过基于对数的语义距离度量来实现，该语义距离度量被设计为自动标记帧之间的相似性。对于AV中的逐渐过渡镜头分割，提出了一种共同投票方法来确定输入帧所属的成员资格。

### Dataset
作者自己航拍的数据集， 28个（23Train， 5Test）， 每个约1分钟， 每个视频下采样为 3fps (每个视频约200帧)。

All ground truth annotations of the AVs are annotated by users. 注释标准呢？ 没说。。。

视频帧大小Resize为96*96， 只取Gray Channel.


### 评价指标

#### 1.F-measure

#### 2.compression ratio (CR)

跟三个有代码的方法进行比较。  
