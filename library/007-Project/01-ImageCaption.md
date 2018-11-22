# Image Caption
>keywords: image, caption, tutorial  
>创建时间： 2018-10-14

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>


这篇笔记是分析 image caption 任务的一个教程的代码解析。我好奇于这是否有一篇来源Paper，我并未找到。

**pytorch_tutorial**：[image_captioning](https://github.com/yunjey/pytorch-tutorial/tree/master/tutorials/03-advanced/image_captioning)  
**知乎教程**： [https://zhuanlan.zhihu.com/p/36282198](https://zhuanlan.zhihu.com/p/36282198)  
**MyCode**: [https://github.com/huangtao36/ImageCaptionTutorial](https://github.com/huangtao36/ImageCaptionTutorial)  

## 环境

### 主要依赖环境：

- COCO Toolkit
- NLTK

### 环境相关材料：  

#### COCO Toolkit 安装   
COCO API: [https://github.com/cocodataset/cocoapi](https://github.com/cocodataset/cocoapi)
COCO Toolkit提供的是封装好的Load Coco数据的类，其内部只是把数据结构化，而不做任何的预处理。  
如果只需要某些简单的Load实现，也是可以自己写代码的。  
Github代码实例： [CocoApi_Check.ipynb](https://github.com/huangtao36/ImageCaptionTutorial/blob/master/jupyter_notebook/CocoApi_Check.ipynb)  
内网代码链接： [CocoApi_Check.ipynb](http://110.65.102.123:8900/notebooks/MyProject/tao/Image_caption/image_captioning/CocoApi_Check.ipynb)

#### NLTK 安装

可直接 pip install nltk
之后需要根据自己的需求下载相应的语言包：   

```  
import nltk
nltk.download('punkt') # 这里只需要‘punkt’这个包
```  

NLTK 官网：[https://www.nltk.org/](https://www.nltk.org/)  
里面有相应的教程与API

## COCO数据集

官网： [COCO caption](http://cocodataset.org/#captions-2015)   

- train: 82783张图片  
- val: 40504张图片  
- test: 40775张图片  
Note: train与val每张图片不少于5个 caption 句子，test数据没有caption（好像后来有了，未确认，官网没有看到有直接的下载链接）

## 构建词库

使用COCO数据集中的annotation信息构建词库

Github代码： [build_cocab.ipynb](https://github.com/huangtao36/ImageCaptionTutorial/blob/master/jupyter_notebook/build_cocab.ipynb)
内网代码链接： [build_cocab.ipynb](http://110.65.102.123:8900/notebooks/MyProject/tao/Image_caption/image_captioning/build_cocab.ipynb)

注意，词库是包含标点符号的，除了 annoatations 中出现次数小于4次的字符串，都会被用作构建词库，所以‘,’以及‘.’等常用标点符号也是有的。

## Dataloader
导入数据的时候需要注意两点。  

1. 导入Vocabulary类， 因为构建的词库是Vocabulary结构的，而pytorch中自身没有这种结构类型的数据。  
2. 重建collate_fn， collate_fn是torch.utils.data.DataLoader中的一个参数，是一个batch处理函数，官方ducuments的描述是： collate_fn (callable, optional) – merges a list of samples to form a mini-batch.  
需要重构是因为一个batch内的caption长度并不一致，需要进行 **排序** 以及 **padding** 的操作。  

当要处理一个全新的数据集的时候，往往需要改写重建collate_fn这个函数，一般的，当无法保证所有数据都有用的时候，也可以改写这个函数进行数据清洗。

内网代码： [train_demo](http://110.65.102.123:8900/notebooks/MyProject/tao/Image_caption/image_captioning/train_demo.ipynb)

## Model

内网代码： [model](http://110.65.102.123:8900/notebooks/MyProject/tao/Image_caption/image_captioning/model.ipynb)

直接看图

![model1](https://i.imgur.com/TT9Ryav.jpg)  

模型的结构还是比较简单的，包含 Encoder 和 Decoder 两个部分。
Encoder 部分：输入是RGB的图片，使用 ResNet-152 预训练的模型得到 feature，删除最后一层，添加一层linear层，最后输出 [batch, 256]的向量。
Decoder 部分：主要的模块就是 LSTM 和 linear (图中与代码不符，没有使用 Softmax)。但其操作较为复杂, 看下图：

![model2](https://i.imgur.com/ASrf39R.jpg)

Decoder 的输入主要有三个部分，Encoder 输出的 feature、Caption 以及 lengths(看图中解释)。其中 Caption 会先经过 Embedding 操作，根据词典的大小 embed 为 [batch, seq\_len, 256] 的向量，注意，这里的 seq\_len 由一个 batch 内最长的那个 caption 决定。   
之后 Caption 跟 image feature 在维度 1 上 cat 起来，成为 [batch, seq_len+1， 256] 的向量。  

关于pack and pad 的操作，可看这个Blog： [pytorch对可变长度序列的处理](https://www.cnblogs.com/lindaxin/p/8052043.html)  

放一个有助理解的图：
<img src="https://i.imgur.com/Pc3qNwb.png" style="zoom:70%" />

Decoder 最后的输出是 [sum(lengths), len(vocab)], sum(lengths) 是一个 batch 内的单词总数，len(vocab) 是词库的数量。转换一下，起始就是 sum(lengths) 个 [1, len(vocab)]。 每个 [1, len(vocab)] 表示一个单词，其中每一个数表示单词库中对应位置单词被选中的概率，最大值对应的位置的词就是最应选择的词。类似于多分类任务，有 len(vocab) 个类别。  

**训练时的 Decoder 流程**：  
TODO: 插入自己画的图

## Loss

 由以上可知，decoder 最后输出的是 [sum(lengths), len(vocab)] 的向量，那么它的 target 是什么？之前也说，这是类似于多分类问题的任务，那么它的 target 也是 [sum(lengths)] 的向量。每一个 target 中的数都对应一个 [1, len(vocab)] 的向量，使用交叉熵计算它们之间的 loss。

## 测试

由 Model 部分可知， Decoder 的操作是融合了 Caption 信息的，这样肯定不能满足测试的要求。测试要求只通过一个 image，经过 Encoder-Decoder，输出 caption 的 id，这个 id 对应词典便可以得到相应的单词。
下面是测试过程中 Decoder 部分的代码：

![Decoder](https://i.imgur.com/qcySSwV.png)  

## Decoder时训练与测试的不同

直接看图  
![Decoder](https://i.imgur.com/8rTVk9M.png)


## Result

![Result](https://i.imgur.com/xvvDXNI.png)

这个结果可以说是很不错了， 我还怀疑是不是我拿错了训练数据了，检查了一遍，这是在COCO训练集上训练的， 5张图片均来自于验证集。

## 评估方式

四种评估方式： BLEU, METEOR, ROUGE, CIDEr

### BLEU
Blog: [机器翻译自动评估-BLEU算法详解](https://blog.csdn.net/qq_31584157/article/details/77709454)  
简书： [机器翻译质量评测算法](https://www.jianshu.com/p/15c22fadcba5)  
MyCode: [ImageCaptionTutorial/evaluate](https://github.com/huangtao36/ImageCaptionTutorial/tree/master/evaluate)  
内网代码： [BLEU](http://110.65.102.123:8900/notebooks/MyProject/tao/Image_caption/image_captioning/evaluate/BLEU.ipynb)


其他方法待续…………

## 问题

### 问题一: 一个batch内不同长度的caption处理

caption 信息经过编码后变成一串数据，对应字典中的一个单词。 caption 是不固定长度的，有的比较长，有的比较短，那么一个 batch 内的长度如何确定呢？  

Ans:  caption 编码之后，其长度为单词数+2（首尾标签），一个 batch 内的 caption 是根据最长的那个 caption 决定的，其余的均补 0 到相同的长度，需要注意的是，一个 batch 内的 caption 是经过排序的，最长-->最短，顺序是固定的，排序的目的是pack_padded_sequence的要求。与此同时，loader 数据的时候，除了 Image, 编码后的 Caption，还有一个是 caption 的有效的长度列表，列表的 size 自然就等于 batch 数了。    

比如如果batch为5的话，caption结构会如下所示：

    tensor([[1, 4, 92,  225,   40,   33,  437,   14,4,  881,  208,  264,  372,  717,  112,4,  843,  418,4, 1609,2],
    		[1, 4, 199,   22,4,  682,   14, 2906,   87, 6829,7, 2520,  365,  162,4,  382,   14, 1540,   19,2,0],
    		[1, 4, 3697,   55,  783,   22,4, 1327,7, 1145, 4962,  162,  33, 8853,   14,4, 1537,   19,2,0,0],
    		[1, 4, 170,  131,  361,4,  217,   14,  383,   22,  525,  170,  171,  365,  162,  366,   19,2,0,0,0],
    		[1, 367, 1514, 4308,   40,   33,  615,   34,7,4,  170,   54,  78,  122,   14,  718,   19,2,0,0,0]],
       device='cuda:0')

其中1是起始标签， 2是结束标签，0是无效位。可以看到，第一行是没有无效位的，之后逐渐增多。
lengths列表则为：

    [21, 20, 19, 18, 18]

注意，起始位是包含在有效位的。

### 问题二： Decoder部分，LSTM之前的操作
稍微画个图来解释一下：  
![Pack](https://i.imgur.com/ad7XMvk.jpg)

我们假设这里的 batch_size = 3, 一个 batch 内的 Caption 的最大有效长度为 5（一般都在20以上，COCO官方要求不少于8个词， 这里只为方便画图） :

- Image: [3, 1, 256]
- caption: [3, 5, 256]
- lengths: list， 3个数

这一部分的操作是为了输入nn.LSTM而做的，以此大概可以知道一个batch内LSTM的工作流程，但怎么样去跟踪这个问题，我就不知道了。
