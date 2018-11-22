# TVSum Dataset
>keywords: tvsum, dataset  
>创建时间：2018-09-11

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

## 基本介绍

**Github**: <https://github.com/yalesong/tvsum>  

TVSum 数据集是一个 video summary task 的专用数据集。 共50个视频，每个视频有20个人标注的1-5的重要性分数。5分最高，2秒为一个标注段。 帧率约为23 - 29帧， 帧数最低2500， 最高约20,000.

## fps与frames

| 视频文件 | fps | frames | 视频文件 | fps | frames |
| :-----: | :----: | :----: | :----: | :----: | :----: |  
| AwmHb44_ouw |29 |10597 | 98MoyGZKHXc |25 |4688 |
| J0nA4VgnoCo |23 |14019 | gzDbaEs1Rlg |25 |7210 |
| XzYM3PfTM4w |29 |3327 | HT5vyqe0Xaw |29 |9671 |
| sTEELN-vY30 |30 |4468 | vdmoEJ5YbrQ |29 |9870 |
| xwqBXPGE9pQ |29 |7010 | akI8YFjEmUw |29 |3995 |
| i3wAGJaaktw |29 |4700 | Bhxk-O1Y7Ho |29 |13511 |
| 0tmA_C6XwfM |25 |3532 | 3eYKfiOEJNs |25 |4853 |
| xxdtq8mxegs |30 |4324 | WG0MBPpPC6I |23 |9535 |
| Hl-__g2gn_A |23 |5846 | Yi4Ij2NM7U4 |24 |9731 |
| 37rzWOQsNIw |29 |5742 | LRw_obCPUt0 |23 |6241 |
| cjibtmSLxQ4 |29 |19406 | b626MiF1ew4 |24 |5661 |
| XkqCExn6_Us |29 |5631 | GsAD1KT1xo8 |29 |4356 |
| PJrm840pAUI |24 |6580 | 91IHQYk1IQM |29 |3312 |
| RBCABdttQmI |29 |10917 | z_6gVvQb2d0 |29 |8281 |
| fWutDQy1nnY |29 |17527 | 4wU_LUjG5Ic |23 |4005 |
| VuWGsYPqAX8 |25 |5412 | JKpqYvAdIsw |25 |3802 |
| xmEERLqJ2kU |29 |13365 | byxOvuiIJV0 |23 |3705 |
| _xMr-HKMfVA |29 |4463 | WxtbjNsCQ8A |29 |7959 |
| uGu_10sucQo |24 |4009 | EE-bNr36nyA |29 |2941 |
| Se3oxnaPsz0 |30 |4166 | oDXZc0tZe04 |29 |11414 |
| qqR6AEXwxoQ |29 |8073 | EYqVtI9YWJA |29 |5939 |
| eQu1rNs0an0 |29 |4931 | JgHubY5Vw3Y |29 |4304 |
| iVt07TCkFM0 |23 |2500 | E11zDS9XGzg |30 |15307 |
| NyBmCxDoHJU |25 |4740 | kLxoNp-UchI |29 |3896 |
| jcoYJXDG9sw |29 |5971 | -esJrBWj2d8 |30 |6912 |  

Note:fps向下取整了

## 加载数据集代码
