---
title: Python学习笔记2--函数
date: 2018-04-08 17:51:26
tags: Python
categories: 编程语言
---

###  python与c的比较学习
## 函数
[**你们要的函数库**](http://docs.python.org/3/library/functions.html#abs)

**或者用help(abs)查看函数信息**

<!-- more -->

#### 定义函数
* def function(x):...   －定义一个函数
* from (文件名，不含.py) import function  －从本地导入函数
* 定义一个空函数
```markdown
def nop():
    pass
```

* pass可以用来作为占位符
```markdown
if age >= 18:
    pass
```

* 数据类型检查可以用内置函数isinstance()实现：
```markdown
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError('bad operand type')
    if x >= 0:
        return x
    else:
        return -x
```

* 函数可以同时返回多个值，但其实就是一个tuple

#### 函数参数
* 默认参数
```markdown
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

* 定义默认参数要牢记一点：默认参数必须指向不变对象,不能有[　],可以这样操作一波
```markdown
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

* 可变参数(参数numbers接收到的是一个tuple)
```markdown
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

* 关键字参数(关键字参数在函数内部自动组装为一个dict)
```markdown
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```
** kw获得的dict是一份拷贝，对函数外的kw没有影响　**

* 命名关键字参数　－限制关键字参数的名字
```markdown
def person(name, age, *, city, job):
    print(name, age, city, job)
```

#### 参数组合（必选参数、默认参数、可变参数、关键字参数和命名关键字参数）
```markdown
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
```

* 对于任意函数，都可以通过类似func(*args,**kw)的形式调用它，无论它的参数是如何定义的
* *args是可变参数，args接收的是一个tuple；
* **kw是关键字参数，kw接收的是一个dict。

#### 递归函数
* 使用递归函数需要注意防止栈溢出，下面的函数n最大为998,亲测
```markdown
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
```

* 尾递归，在函数返回的时候，调用自身本身

```markdown
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```

* 尾递归调用时，如果做了优化，栈不会增长，因此，无论多少次调用也不会导致栈溢出。
* 大多数编程语言没有针对尾递归做优化，Python解释器也没有做优化，所以，即使把上面的fact(n)函数改成尾递归方式，也会导致栈溢出。
** 综上，尾递归这一节说的都是屁话**

[**递归实现汉罗塔**](http://bbs.fishc.com/thread-61965-1-1.html)
** 感觉有一点动态规划的感觉**