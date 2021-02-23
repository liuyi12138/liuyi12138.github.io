---
title: Python学习笔记3--高级特性
date: 2018-04-08 17:52:33
tags: Python
categories: 编程语言
---

### python与c的比较学习

## 高级特性
<!--more--> 

### 切片(slice)
* L[a:b:c] －取list中的索引a-(b-1)的元素,索引一次增加c(一共b-a个元素)(c可以省略)(c可以为负数哦)
* 如果第一个索引为0，可以省略
* 甚至支持倒切片，倒数第一个元素索引为-1，(此时b可以省略)
* 甚至什么都不写，只写[:]就可以原样复制一个list
* tuple切片仍然是tuple,字符串切片仍然是字符串

### 迭代
* 默认情况下，dict迭代的是key
* 要迭代value，可以用for value in d.values()
* 要同时迭代key和value，可以用for k, v in d.items()

### 列表生成器(List Comprehensions)
* list(range(1, 11)) －[前面见过的哦，文章底部](https://liuyi12138.github.io/2018/01/08/Python%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B01--%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95/)
* 列表生成式
```markdown
[x * x for x in range(1, 11)]
[x * x for x in range(1, 11) if x % 2 == 0]
[m + n for m in 'ABC' for n in 'XYZ']
[k + '=' + v for k, v in d.items()]
[s.lower() for s in L]
```

* 列出当前目录下的所有文件和目录名
```markdown
>>> import os # 导入os模块，模块的概念后面讲到
>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
```

### 生成器(generator)
* 创建一个generator
 * 把一个列表生成式的[]改成() 　　　g = (x * x for x in range(10))
 * 可以通过next()函数获得generator的下一个返回值，更多的时候用for循环
```markdown
g = (x * x for x in range(10))
for n in g:
　　　print(n)
```

 * 斐波拉契数列生成器
```markdown
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'

```

[杨辉三角生成器](http://blog.csdn.net/qq_33528613/article/details/72861178)
**变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行**

* 用for循环调用generator时拿不到generator的return语句的返回值,如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中

### 迭代器
* 可以直接作用于for循环的对象统称为可迭代对象：Iterable
* 集合数据类型:list,tuple,dict,set,str...(int不是interable)
* generator:生成器，带yield的generator function
* isinstance([], Iterable) 　－可以使用isinstance()判断一个对象是否是Iterable对象
* Python的for循环本质上就是通过不断调用next()函数实现的，例如：
* 可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator
* isinstance((x for x in range(10)), Iterator)

**注意Interanle和Interator,前者是可迭代对象，后者是迭代器**

*　把list、dict、str等Iterable变成Iterator可以使用iter()函数
*　Python的Iterator对象表示的是一个数据流，它的计算是惰性的，只有在需要返回下一个数据时它才会计算，甚至它可以表示一个无限大的数据流