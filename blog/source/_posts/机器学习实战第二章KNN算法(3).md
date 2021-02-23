---
title: 机器学习实战第二章KNN算法(3)
date: 2018-04-08 17:56:04
tags: 机器学习
categories: 机器学习
---

归一化特征值
测试算法
预测函数
python 代码注释

<!-- more -->

## 归一化特征值
### knn.py
```markdown
def autoNorm(dataSet):
    minVals = dataSet.min(0)	#找出样本集中的最小值
    maxVals = dataSet.max(0)	#找出样本集中的最大值
    ranges = maxVals - minVals	#最大最小值之间的差值
    normDataSet = zeros(shape(dataSet))	#创建与样本集一样大小的零矩阵
    m = dataSet.shape[0]
    normDataSet = dataSet - tile(minVals, (m,1))	#样本集中的元素与最小值的差值
    normDataSet = normDataSet/tile(ranges, (m,1))	#数据相除，归一化
    return normDataSet, ranges, minVals
```

## 测试算法
### knn.py
```markdown
def datingClassTest():
    hoRatio = 0.10	＃ #选取多少数据测试分类器
    datingDataMat,datingLabels = file2matrix('datingTestSet2.txt')    #从datingTestSet2.txt中获取数据
    normMat, ranges, minVals = autoNorm(datingDataMat)    #归一化数据
    m = normMat.shape[0]
    numTestVecs = int(m*hoRatio)    #设置测试个数
    errorCount = 0.0    #记录错误数量
    for i in range(numTestVecs):        #分类算法
        classifierResult = classify0(normMat[i,:],normMat[numTestVecs:m,:],datingLabels[numTestVecs:m],3)
		 print "the classifier came back with: %d, the real answer is: %d" % (classifierResult, datingLabels[i])
        if (classifierResult != datingLabels[i]): errorCount += 1.0
    #计算错误率
    print "the total error rate is: %f" % (errorCount/float(numTestVecs))
    print errorCount
```


## 预测函数
### knn.py
```markdown
def classifyPerson():
    resultList = ['not at all', 'in small doses', 'in large doses']
    percentTats = float(input("percentage of time spent playing video games ?")) 
    #raw_inout函数允许用户输入文本行命令并返回用户所输人的命令
    ffMiles = float(input("frequent filer miles earned per year?"))
    iceCream = float(input("liters of ice cream consumed per year?"))
    datingDataMat, datingLabels = file2matrix('datingTestSet2.txt')
    normMat, ranges, minVals = autoNorm(datingDataMat)
    inArr = array([ffMiles, percentTats, iceCream])
    classifierResult = classify0((inArr-minVals)/ranges,normMat,datingLabels, 3)
    print("You will probably like this person: ", resultList[classifierResult - 1])
```
### 错误解决
**python3.0版本后用input替换了raw_input**