# Video Summarization with Long Short-term Memory
>keywords: video, summary, LSTM, DPP  
>创建时间：2018-09-09

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

这是近年 Video Summary Task 的经典 Paper 之一。主要研究如何用 LSTM (BiLSTM) 对 Video sequence 做 supervised learning, 以得到 video 的 summary ， 以及结合 Determinantal Point Process(DPP) 做的改进。

Paper: <https://arxiv.org/pdf/1605.08110.pdf>  
Github: <https://github.com/kezhang-cs/Video-Summarization-with-LSTM>

## Introduction

Video Summary 的 motivation 是在一段长视频中提取出最为关键的部分，以代表此视频的内容。这个‘最关键的部分’有两种表现形式：

1. **keyframe**，既是抽取出视频中最为关键的几个帧（几张图片）来代表这段视频，一般抽取出的keyframes应在10张以内，依据视频复杂度以及时长而变；
2. **key subshot**，既是截取视频中的几个小片段来表示这段视频，抽取的长度约定为**15%**的原视频长度 (基本上都以此为标准，猜测是因为tvSum数据集的界定，而后就沿用了)。

Video Summary 方法分为了 Unsupervised 和 Supervised 两种，文中认为 Unsupervised 的方法比较 Intuitive。 Supervised 的方法 more aligned with human semantic understanding。 同时基于视频是有时序特征的，需要考虑帧之间的 Inter-dependency, 所以本文使用的是基于 LSTM 的 监督学习方法。

## Approach
### Problem Statement

**Input:** video sequence。 一般不会每一帧都取，这样数据量太大了，此外，大部分的方法都会使用预训练的网络对这个video seq做一个预处理（例如使用GoogLeNet），然后使用video feature seq 作为网络的输入。 定义为:
$$x = \{ {x_1},{x_2}, \ldots ,{x_t}, \ldots ,{x_T}\}$$

**Output:** 也即是上面的两种‘最关键的部分’的表现形式。  
1. *Selected Keyframes*  
2. *Interval-based Keyshots*

TODO:解释Input选取帧的方法，以及output的具体数据形式。

### vsLSTM for Video Summarization
<img src="https://i.imgur.com/J2jcynm.jpg" style="zoom:60%" />

这个方法主要是 BiLSTM + MLP 的结合, 不同的是图中红色箭头指向的那条线。  这里的MLP到底是怎么定义的， 看代码也没能理解，一般而言，会加一两个Linear层再加一个激活层。（我发觉 Theano 的语法是真难看！！！）  

定义为：
$${y_t} = {f_1}({h_t}^{forward},{h_t}^{backward},{x_t})$$  


文中提到，输出的 $y$ 既可以是连续的帧级重要性分数，也可以是离散的二进制 0，1 标签。怎么跟 MLP 对应起来是个问题，应该还有后续操作。 **这里的不同输出肯定会影响到loss的计算方式，且看下文**


### Enhancing vsLSTM by Modeling Pairwise Repulsiveness
vsLSTM 模型需要可以判断一个帧在 Summary 中的重要性/相关性，因此引入了 determinantal point process (DPP)  来 model pairwise frame-level “repulsiveness”。 通过消除冗余帧的方式来增加其选择的多样性。

Note that diversity can only be measured “collectively” on a (sub)set of (selected) frames, not on frames independently or sequentially.

The directed sequential nature in LSTMs is arguably weaker in examining all the fames simultaneously in the subset to measure diversity, thus is at the risk of having higher recall but lower **precision**. On the other hand, DPPs likely yield low **recalls** but high precisions. In essence, the two are complementary to each other. （要理解这段话，需要先知道VS Task 的评估方式）

### Determinantal point processes (DPP)

DPP, 中文称：行列式点过程。  
资料：  
1. 知乎：<https://www.zhihu.com/question/62962281>  
2. Github: <https://github.com/mehdidc/dpp>  
3. Wiki: <https://en.wikipedia.org/wiki/Point_process>  

公式： $$P(z\subset Z;L) = \frac{\left|{L_z}\right|}{\left|{L+I}\right|}$$  
其中：  
--$Z$: 可理解为N帧的视频序列集合  
--$z$: $Z$ 的子集，既是视频的某几帧的集合  
--$L$: 视频帧之间的相似性矩阵，N * N, 主对角线为0，  
--$P$: 即为$z$被选中的概率。  
--$I$: 单位矩阵

简单描述为： 子集$z$被选中的概率，等于其相应相似性矩阵的行列式比上整体相似性矩阵加上单位矩阵的行列式。

如果选中的子集中有两个帧很像，那么其相似矩阵中就会有两行或两列是一模一样的，那么其行列式就会等于0，致使其概率为0。 全局的相似性矩阵$Z$中，使用一个单位矩阵在控制着，使其不为0。 （文中说会有相同的行和列，虽然也没错，但感觉表述不准确！）

那么如此，要得到高的概率，子集中的差异性就应该越高，这样得到的行列式的值才会大。  

TODO: 这只是根据论文的粗浅理解，弄个伪代码试试！

### dppLSTM
