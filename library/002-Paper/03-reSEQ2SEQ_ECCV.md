# Retrospective Encoders for Video Summarization
>keywords: video, summary, eccv  
>创建时间：2018-09-18

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>


## 简述
这篇文章的关键思想是——测量生成的摘要在抽象语义空间中与原始视频的相似程度，评估摘要“整体”是否保留了原始视频中最重要、最相关的信息。

>Paper: <http://www.cs.utexas.edu/~grauman/papers/reseq-eccv2018.pdf>  
Supplementary: <http://www.cs.utexas.edu/~grauman/papers/reseq-eccv2018-supp.pdf>  
Code: None  

```  
@inproceedings{zhang2018retrospective,
  title={Retrospective Encoders for Video Summarization},
  author={Zhang, Ke and Grauman, Kristen and Sha, Fei},
  booktitle={Proceedings of the European Conference on Computer Vision (ECCV)},
  pages={383--399},
  year={2018}
}
```  

## Model
**Input:** Frames levels features, [seq, 1024], pretrain on GoogLeNet(penultimate layer-pool 5).  
每一个LSTM隐藏层输出都是 256.  
文中没有给出最后的输出形式，即最后是如何获得key shots的（或是我忽略了）。一个一般的做法是输出 frames-based 的 scores, 根据先前分割的shots的长度，以及每一个shot的平均分，使用背包算法获得原视频时间长度15%的视频key shots作为摘要。

但看图来说，Decoder 部分难以解释为什么一个LSTM输出一个Keyshots.

### Fig1
Fig.1 是一个简单的概念图，在一般的摘要生成模型后再加入一个 **‘Retrospective Encoder’**, 生成summary的Encoder-Feature, 然后分别将原视频与Summary的Feature Embedding到相同的维度，即可进行对应点的similarity计算（欧式距离）。

![re_SEQ2SEQ_fig1](https://i.imgur.com/YT1GpPK.jpg)

这为半监督学习提供了一个自然的基础，可以利用大量未标记的视频来加强训练。  

### Fig2
![re_SEQ2SEQ_fig2](https://i.imgur.com/NvgHBMR.jpg)

Fig.2 中，SEQSEQ部分，虽不是本文的贡献点，但值得注意的是他在每一个视频段的分界点输出 LSTM 的 $h_t$ ,那么这个分界点怎么来，具体在[Implement-Shot Boundaries](#Implement)中解释。  

### Regression loss
Regression loss function for matching summaries.  这个损失函数用于最大化选择的摘要的准确率。

$$
{\ell_{SUMMARY}}=\sum\limits_i^L{\left\|{y_l-g_l}\right\|_2^2}
$$

其中：  
: $y_l$ is visual feature vectors (of shots).  
: $g_l$ is a target vector corresponding to the $l$-th shot in the ground-truth summary $Z$.  


Regression loss中：

$$
{g_l}=\left[{s_{b_l}\;{\bar x}_{b_l}}\right]
$$


意思就是 $g_l$ 是两个向量的拼接，一个是单个 shot 中所有帧的 feature 的平均，二是 Encoder 第一层的每一个 shot 的输出。  

**原文中： compose $g_l$ as the concatenation of two vectors, 这里 $y_l$ 和 $g_l$ 怎么保持数据长度一致？？？ 同时为什么这个loss计算会跟 Encoder 的输出有关，这难道不是在增加固有误差吗？**

### Embedding Matching
Embedding matching for the summary and the original.  
$${\ell_{MATCH}}=\left\|h_B^(2)-u_L\right\|_2^2$$

这个公式直接对应于Fig. 2中 **Retrospective  Encoder** 的 $d$.
既是本文的核心思想，需要注意的是 $h_B^(2)$ 是 Encode LSTM 的最后一个输出（final hidden state）.

### Embedding Mis-matching
Contrastive embedding for mismatched summary and original.

懒得敲，直接放图。
![re_SEQ2SEQ_eq4](https://i.imgur.com/TD6BWUK.jpg)

简单来说就是，Embedding Matching 中不是只用了最后一层LSTM嘛，这里把其余的每一层的输出都给用了一遍。

## <span id="Implement">Implement</span>
这一部分补充一些 Model 的额外操作。

### Shot Boundaries
文章中的补充材料中说使用 CoSum 数据集，Pretrain 的 单层的LSTM shot-boundary dectection model 用于边界检测。

>CoSum has 51 videos with human annotated shot-boundaries. The LSTM has 256−dim hidden units and is trained for 150 epochs with learning rate 4e-4. We then threshold the predictions on any new videos to detect the boundaries. The best thresholds are determined by the summarization performance on the validation set.

不同的是，一般而言，大部分的Video Summarization Paper 都会用到 **KTS** 算法，用于视频的 temporal segments. 来源 Paper(ECCV2014): [Category-specific video summarization](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.653.7638&rep=rep1&type=pdf), 本文也对比了这两种方法的实验结果。

## Experiment

xx

## My Summarize
1. 文章中提到，LSTM 模型的理想视频输入长度小于100帧,故而利用了LSTM层级结构来 Capture 长期的时间依赖关系。   
2. 使用 CoSum 数据集训练一个边界检测器。 虽然文章没有给出代码，但训练一个模型我认为比直接使用 KTS 切割来得更可靠些。
3. 实验介绍中，使用 t-SNE 方法将视频语义编码可视化，很直观。 （原文Fig. 4）
4. 实验的有些细节还是没有懂，比如 Decoder 部分没有做详细的介绍（或是我没有找到）
