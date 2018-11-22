# Video Summary方向的最新Paper与其相关
>keywords: paper, video, summary  
>创建时间：2018-09-17  

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

Video Summary 方向近两年的文章主要集中在三大计算机视觉会议上，其次是Arxiv，期刊上较少。

## Paper
### 1. re-SEQ2SEQ, ECCV2018
这篇文章提出了一种新的loss，用于衡量预测的摘要是否保留与原始视频中相同的信息。使用‘retrospevtive encoder’将预测的摘要embeds into an abstract semantic space，同时embeds the original video in the same space，最小化匹配对之间的距离，最大化不匹配的距离。  

Code: None  
Dataset：Summe, TVSum，VTW

```
@inproceedings{zhang2018retrospective,
  title={Retrospective Encoders for Video Summarization},
  author={Zhang, Ke and Grauman, Kristen and Sha, Fei},
  booktitle={Proceedings of the European Conference on Computer Vision (ECCV)},
  pages={383--399},
  year={2018}
}
```  

### 2. sum-GAN, CVPR2017
使用GAN的方法，做无监督的vs task。

Dataset：SumMe, TVSum, OVP, Youtube  
Code: <https://github.com/j-min/Adversarial_Video_Summary>

```  
@inproceedings{mahasseni2017unsupervised,
  title={Unsupervised Video Summarization With Adversarial LSTM Networks},
  author={Mahasseni, Behrooz and Lam, Michael and Todorovic, Sinisa},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={202--211},
  year={2017}
}
```  

### 3. VESD, ECCV2018
这个文章使用了从youtobe中自己爬下来的视频，经过处理用于训练。 是个增加数据集的方向！

Code(Available soon, -_-): <https://github.com/cssjcai/vesd>  
Dataset： CoSum, TVSum

```  
@inproceedings{cai2018weakly,
  title={Weakly-supervised Video Summarization using Variational Encoder-Decoder and Web Prior},
  author={Cai, Sijia and Zuo, Wangmeng and Davis, Larry S and Zhang, Lei},
  booktitle={Proceedings of the European Conference on Computer Vision (ECCV)},
  pages={184--200},
  year={2018}
}
```  

### 4. Enhancing VS via Vision-Language Embedding，CVPR2017

Code: None  
Dataset: UT Egocentric, TV Episodes

```
@inproceedings{plummer2017enhancing,
  title={Enhancing video summarization via vision-language embedding},
  author={Plummer, Bryan A and Brown, Matthew and Lazebnik, Svetlana},
  booktitle={Computer Vision and Pattern Recognition},
  volume={2},
  year={2017}
}
```  

---
## Dataset
### 1. Summe
xxx

### 2. TVSum
xxx

### 3. VTW
large-scale and originally proposed for video highlight detection. re-targeted for video summarization by converting the highlights into keyshots (H-RNN：Zhao, B., Li, X., Lu, X.: Hierarchical recurrent neural network for video summarization. In: ACM MM (2017))   
annotated only for keyshot.

### 4. OVP
open Video Project

### 5. Youtube
xxx

### 6、coSum
xxx

---
## 会议/期刊
### 会议

- ICCV（奇数年）
- CVPR（每年）
- ECCV（偶数年）

### 期刊

- T-PAMI: IEEE Transactions on Pattern Analysis and Machine Intelligence--1 区
- IJCV: International Journal of Computer Vision--1 区
- T-IP: IEEE Transactions on Image Processing--2 区
- PR: Pattern Recognition--2 区
- Multimedia Tools and Applications--《A novel clustering method for static video summarization，2017》--4 区
- IEEE Transactions on Circuits and Systems for Video Technology--《Perceptual video summarization—A new framework for video summarization，2017》--2 区
- Association for Computing Machinery--《Music-Guided Video Summarization using Quadratic Assignments，2017》
- Journal of Ambient Intelligence and Humanized Computing--《Creating personalized video summaries via semantic event detection，2018》--4 区
