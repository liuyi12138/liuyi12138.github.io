---
title: 机器学习实战第二章KNN算法(2)
date: 2018-04-08 17:55:43
tags: 机器学习
categories: 机器学习
---

将文本记录转换为NumPy的解析程序  
将txt数据转换为numpy数组，将特征值与标签分离  
使用Matplotlib画图分析数据  
python 代码注释

<!--more--> 
## knn.py
```markdown
def file2matrix(filename):
    fr = open(filename)
    arrayOLines = fr.readlines() #读入所有行
    numberOfLines = len(arrayOLines) #行数
    returnMat = zeros((numberOfLines, 3))  #创建数组，数据集  zeros功能是返回一个m×n×p×...的double类零矩阵
    classLabelVector = [] #标签集
    index = 0
    for line in arrayOLines:
        line = line.strip()   #移除所有的回车符  strip函数见注释(1)
        listFromLine = line.split('\t')  #把一个字符串按\t分割成字符串数组  split函数见注释(2)
        returnMat[index,:] = listFromLine[0:3] #取listFromLine的前三个元素放入returnMat
        classLabelVector.append(int(listFromLine[-1])) #选取listFromLine的最后一个元素依次存入classLabelVector列表中
        index += 1
    return returnMat, classLabelVector
#datingDatamat为特征数据集，datingLabels为标签集

datingDataMat, datingLabels = file2matrix('datingTestSet2.txt')
#datingTestSet2.txt文件中每行有4个数据，前三个为对象特征值，第四个为对象标签。
```

## 注释部分
### 1.strip函数
函数原型
声明：s为字符串，rm为要删除的字符序列
s.strip(rm)        删除s字符串中开头、结尾处，位于 rm删除序列的字符
s.lstrip(rm)       删除s字符串中开头处，位于 rm删除序列的字符
s.rstrip(rm)      删除s字符串中结尾处，位于 rm删除序列的字符

**当rm为空时，默认删除空白符（包括'\n', '\r',  '\t',  ' ')**
**这里的rm删除序列是只要边（开头或结尾）上的字符在删除序列内，就删除掉。**

### 2.split函数
#### split()就是将一个字符串分裂成多个字符串组成的列表
* split()当不带参数时以空格进行分割，当代参数时，以该参数进行分割
* split()会分出空字符元素
```markdown
>>> str="hello boy<[www.doiido.com]>byebye"
>>> str.split("[")[1].split("]")[0]
'www.doiido.com'
>>> str.split("[")[1].split("]")[0].split(".")
['www', 'doiido', 'com']
```

***

## 使用Matplotlib画图分析数据
**暂时不懂贴上代码**

```markdown
import numpy
import matplotlib
import matplotlib.pyplot as plt
import knn
reload(knn)
datingDataMat,datingLabels=kNN.file2matrix('datingTestSet2.txt')
fig = plt.figure()
ax = fig.add_subplot(111)
ax.scatter(datingDataMat[:,1],datingDataMat[:,2],15.0*array(datingLabels),15.0*array(datingLabels))
plt.show()
```

### 补充说明
[python中的读入操作](http://blog.csdn.net/werm520/article/details/6898473)
