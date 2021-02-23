---
title: 机器学习实战第二章KNN算法(1)
date: 2018-04-08 17:55:09
tags: 机器学习
categories: 机器学习
---

最简单kNN算法实现
python 代码注释

<!-- more -->

## knn.py
```markdown
from numpy import *      #导入科学计算报numpy
import operator          #导入运算符模块

def createDataSet():
	group = array([[1.0,1.1],[1.0,1.0],[0,0],[0,0.1]])
    labels = ['A','A','B','B']
    return group,labels

def classify0(inX,dataSet,labels,k):	#inX是需要输入的坐标，dateSet是array,labels是标签，k是k个最邻近数据
	dataSetSize = dataSet.shape[0]		#shape函数见注释(1)
	diffMat = tile(inX,(dataSetSize,1)) - dataSet　　　#tile函数见注释(2)  此处求得两点坐标差值(矩阵形式)
	sqDiffMat = diffMat**2		#坐标差值的平方
	sqDistances = sqDiffMat.sum(axis = 1)	#sum函数见注释(3)  此处得出每一组的坐标差值平方的和
	distances = sqDistances**0.5	#开方，得到距离
	sortedDistIndicies = distances.argsort()	#rgsort函数见注释(4)　　此处将元素按照由小到大的顺序返回下标
	classCount = {}
	for i in range(k):
		voteIlabel = labels[sortedDistIndicies[i]]
		classCount[voteIlabel] = classCount.get(voteIlabel,0) + 1	
#get()函数返回指定键的值，如果值不在字典中返回默认值。这行代码的意思就是算离目标点距离最近的k个点的类别，这个点是哪个类别哪个类别就加1
	sortedClassCount = sorted(classCount.iteritems(),key=operator.itemgetter(1),reverse=True) 
#sorted见注释(5) items方法是可以将字典中的所有项，以列表方式返回。 key=operator.itemgetter(1)的意思是按照字典里的第一个排序，
    {A:1,B:2},要按照第1个（AB是第0个），即‘1’‘2’排序。reverse=True是降序排序
	return sortedClassCount[0][0]		#返回类别最多的类别
```



## 注释部分
### 1.shape函数
#### shape函数是numpy.core.fromnumeric中的函数，它的功能是查看矩阵或者数组的维数。
```markdown
    >>> e = eye(3)
    >>> e
    array([[ 1.,  0.,  0.],
           [ 0.,  1.,  0.],
           [ 0.,  0.,  1.]])
    >>> e.shape
    (3, 3)
```

### 2.tile函数
#### tile函数也是numpy中的函数，用来重复矩阵
```markdown
>>> import numpy
>>> numpy.tile([0,0],5)#在列方向上重复[0,0]5次，默认行1次
array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])
>>> numpy.tile([0,0],(1,1))#在列方向上重复[0,0]1次，行1次
array([[0, 0]])
>>> numpy.tile([0,0],(2,1))#在列方向上重复[0,0]1次，行2次
array([[0, 0],
       [0, 0]])
>>> numpy.tile([0,0],(3,1))
array([[0, 0],
       [0, 0],
       [0, 0]])
>>> numpy.tile([0,0],(1,3))#在列方向上重复[0,0]3次，行1次
array([[0, 0, 0, 0, 0, 0]])
>>> numpy.tile([0,0],(2,3))#在列方向上重复[0,0]3次，行2次
array([[0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0]])
```

### 3.sum函数
#### numpy中的sum与python本身的sum多了一个参数axis,axis＝0表示按列相加，axis＝1表示按照行的方向相加
```markdown
    >>> import numpy as np
    >>> a=np.sum([[0,1,2],[2,1,3]])
    >>> a
    9
    >>> a.shape
    ()
    >>> a=np.sum([[0,1,2],[2,1,3]],axis=0)
    >>> a
    array([2, 2, 5])
    >>> a.shape
    (3,)
    >>> a=np.sum([[0,1,2],[2,1,3]],axis=1)
    >>> a
    array([3, 6])
    >>> a.shape
    (2,)
```

### 4.argsort函数
#### numpy中的argsort函数将元素按照由小到大的顺序返回下标
```markdown
    One dimensional array:一维数组

    >>> x = np.array([3, 1, 2])
    >>> np.argsort(x)
    array([1, 2, 0])

	Two-dimensional array:二维数组

    >>> x = np.array([[0, 3], [2, 2]])
    >>> x
    array([[0, 3],
           [2, 2]])

    >>> np.argsort(x, axis=0) #按列排序
    array([[0, 1],
           [1, 0]])

    >>> np.argsort(x, axis=1) #按行排序
    array([[0, 1],
           [0, 1]])
```

### 5.sorted函数
sortedClassCount = sorted(classCount.iteritems(),key=operator.itemgetter(1),reverse=True)

iterable：是可迭代类型;
cmp：用于比较的函数，比较什么由key决定;
key：用列表元素的某个属性或函数进行作为关键字，有默认值，迭代集合中的一项;
reverse：排序规则. reverse = True  降序 或者 reverse = False 升序，有默认值。
返回值：是一个经过排序的可迭代类型，与iterable一样。

***

### 错误解决

sortedClassCount = sorted(classCount.iteritems(),key=operator.itemgetter(1),reverse=True)
在看机器学习实战的时候 KNN.py的时候会出现报错'dict' object has no attribute 'iteritems'

解决方法：Python3.5中：iteritems变为items

### 补充说明
[python中的常用排序](http://blog.csdn.net/u013731339/article/details/41449177)
[python字典的get函数和iteritems函数](http://blog.csdn.net/yedoubushishen/article/details/51984524)