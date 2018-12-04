## 网络
### urllib
在python3中已整合
#### beautifulsoup4
用于解释提取抓取的html内容
### http.cookiejar
在python3中改为http.cookiejar在python2使用的是cookielib.
### PySpider
爬虫框架,主要的功能需求是：
* 抓取、更新调度多站点的特定的页面
* 需要对页面进行结构化信息提取
* 灵活可扩展，稳定可监控

### requests

## 检查
### re

正则表达式

### chardet
判断编码模块如utf-8等

## 图形
### PIL
PIL（Python Imaging Library Python，图像处理类库）提供了通用的图像处理功能，以及大量有用的基本图像操作，比如图像缩放、裁剪、旋转、颜色转换等。
可以从 http://www.pythonware.com/products/pil/ 下载。
### opencv
图形图像处理

### matplotlib
一个 2D 绘图库，可产生出版物质量的图表。

http://matplotlib.org/

### scikit-image
有一组图像处理的算法，可以使过滤一张图片变得很简单，非常适合用于对图像的预处理。
```
pip install scikit-image --upgrade
```
## 机器学习
### sklearn
sklearn是基于numpy和scipy的一个机器学习算法库，设计的非常优雅，它让我们能够使用同样的接口来实现所有不同的算法调用。
## 数据
### json
json的解释和封装模块

### numpy
非常通用的数学计算库，在机器学习中经常用到。
http://www.numpy.org/

#### loadtxt

载入txt或csv的文本内容,多用于exl的表格数据导入。

#### dot()

返回的是两个数组的点积(dot product)

```python
#如果处理的是一维数组，则得到的是两数组的內积
In: d = np.arange(0,9)
Out: array([0, 1, 2, 3, 4, 5, 6, 7, 8])
In : e = d[::-1]
Out: array([8, 7, 6, 5, 4, 3, 2, 1, 0])

In : np.dot(d,e) 
Out: 84
#如果是二维数组（矩阵）之间的运算，则得到的是矩阵积（mastrix product）所得到的数组中的每个元素为，第一个矩阵中与该元素行号相同的元素与第二个矩阵与该元素列号相同的元素，两两相乘后再求和。
In : a = np.arange(1,5).reshape(2,2)
Out:
array([[1, 2],
       [3, 4]])

In : b = np.arange(5,9).reshape(2,2)
Out: array([[5, 6],
            [7, 8]])

In : np.dot(a,b)
Out:
array([[19, 22],
       [43, 50]])
```

![图片](https://images2017.cnblogs.com/blog/1281542/201711/1281542-20171130030615620-21120532.png)



#### 矩阵操作
一、生成numpy矩阵的几个相关函数：

* numpy.array()

* numpy.zeros()

  生成全为0的矩阵数组。

* numpy.ones()

  生成全为1的矩阵数组。

* numpy.eye() 

  生成对角矩阵


二、串联生成numpy矩阵的几个相关函数：

* numpy.array()
* numpy.row_stack()
* numpy.column_stack()
* numpy.reshape()

```python
>>> import numpy  
>>> numpy.eye(3)  
array([[ 1.,  0.,  0.],  
       [ 0.,  1.,  0.],  
       [ 0.,  0.,  1.]])  
>>> numpy.zeros(3)  
array([ 0.,  0.,  0.])  
>>> numpy.ones(3)  
array([ 1.,  1.,  1.])  
>>> x1 = numpy.array((1, 2, 3))  
>>> x1  
array([1, 2, 3])  
>>> x2 = numpy.array([4, 5, 6])  
>>> x2  
array([4, 5, 6])  
>>> x3 = numpy.array((x1, x2))  
>>> x3  
array([[1, 2, 3],  
       [4, 5, 6]])  
>>> x4 = x3.reshape(2, 3)  
>>> x4  
array([[1, 2, 3],  
       [4, 5, 6]])  
>>> x4 = x3.reshape(3, 2)  
>>> x4  
array([[1, 2],  
       [3, 4],  
       [5, 6]])  
>>> x5 = numpy.row_stack((x1, x2))  
>>> x5  
array([[1, 2, 3],  
       [4, 5, 6]])  
>>> x6 = numpy.row_stack([x1, x2])  
>>> x6  
array([[1, 2, 3],  
       [4, 5, 6]])  
>>> x7 = numpy.row_stack((x6, x2))  
>>> x7  
array([[1, 2, 3],  
       [4, 5, 6],  
       [4, 5, 6]])  
>>> x7[0]  
array([1, 2, 3])  
>>> x7[1]  
array([4, 5, 6])  
>>> x7[2]  
array([4, 5, 6])  
>>> x8 = numpy.column_stack([x1, x2, x1, x2])  
>>> x8  
array([[1, 4, 1, 4],  
       [2, 5, 2, 5],  
       [3, 6, 3, 6]])  
>>> x8[0]  
array([1, 4, 1, 4])  
>>> x8[1]  
array([2, 5, 2, 5])  
>>> x8[2]  
array([3, 6, 3, 6])  
>>> x8[0][3]  
4  
>>>
```



### pandas
Python 数据分析库，包括数据框架（dataframes）等结构 http://pandas.pydata.org/
> 学习资料：http://pandas.pydata.org/pandas-docs/stable/10min.html

### scikit-learn
用于数据分析和数据挖掘人物的机器学习算法，是通用的机器学习库，其覆盖了k近邻算法

http://scikit-learn.org/stable/

### scipy

> 学习资料：http://www.scipy-lectures.org/

### Theano
有效地定义，优化和评估包含多维数组的数学表达式。

## 多媒体
### pdfkit
一个将html网页保存为pdf的模块
### librosa
是用于python进行音频特征提取的第三方库，有很多方式可以提取音频特征。
### nltk
模块中包含着大量的语料库，可以很方便地完成很多自然语言处理的作务，包括分词，词性标注，命名实体识别(NER)及句法分析.