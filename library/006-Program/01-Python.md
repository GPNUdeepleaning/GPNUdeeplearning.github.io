# Python

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

**此笔记包含python的基础语法与常用包的函数**

## jupyter notebook
### plot快速画图
```Python  
"""  
这是将两个列表在一张图上画折线图  
"""  
%pylab inline  
len_ = range(0, len(alist))  
plt.plot(len_, alist1, 'b', len_, alist2, 'r')
```  
## python
### time

```
"""
程序计时
"""
st = time.time()

for i in enumerate(list):
	print("进度:{0}%，已耗时:{1}s".format(round((i + 1) * 100 / size), round(time.time() - st, 2)), end="\r")
```

### sort

```
"""
列表根据字符串中的数字排序（文件名排序）】
"""
sorted(img_file_list, key=lambda i:int(re.match(r'(\d+)',i).group()))
```  

### os

```   
"""  
获取上一级目录  
"""  
os.path.abspath(os.path.join(os.getcwd()))  

"""  
获取上两级目录，  
要获取当前项目目录下前面第几层，就在后面加多少组".../"  
"""  
os.path.abspath(os.path.join(os.getcwd(), "../.."))  
```

## sklearn
### TSNE  
