---
title: Python学习笔记4--函数式编程
date: 2018-04-08 17:53:02
tags: Python
categories: 编程语言
---

###  python与javascript的比较学习
## 函数式编程
<!--more--> 

感觉基本和JavaScript的高阶函数一样，吓得我赶紧复习了一遍js
[复习传送门](https://liuyi12138.github.io/2017/12/22/JavaScript%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%92--%E5%87%BD%E6%95%B0/)

###  高阶函数
** 收回我刚刚的话，py和js的高阶函数只是看起来一样，其实区别贼大**

### map
* js中map的用法　－ r=arr.map(function) 得到一个新的arr
* py中map的用法　－ r=map(f,list) 得到一个Iterator
* list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9])) －因此在py中用list()函数让它把整个序列都计算出来并返回一个list
* js里方法较多，py里函数较多
* 如果在py里像js一样使用map结果是'list' object has no attribute 'map'即py中list没有map方法，当然其他数据结构也没有，亲测

### reduce
* js中reduce的用法　－arr.reduce(function (x, y))
* py中reduce的用法　－reduce(fn, [1, 3, 5, 7, 9])
* 完整用法
```markdown
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```

### filter
* filter()函数返回的是一个Iterator，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list

```markdown
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
```

### sort
* Python内置的sorted()函数就可以对list进行排序
* sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序
* sorted([36, 5, -12, 9, -21], key=abs)  －按绝对值大小排序
* py貌似没有js的那种sort()方法默认把所有元素先转换为String再排序的问题
* 要进行反向排序，不必改动key函数，可以传入第三个参数reverse=True

---

### 返回函数
* py中返回函数或等于js中的闭包
* 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量
* [js中的闭包](https://liuyi12138.github.io/2017/12/22/JavaScript%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%EF%BC%92--%E5%87%BD%E6%95%B0/)

### 匿名函数
* list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
* 匿名函数只能有一个表达式，只能返回该表达式的结果
* 可以把匿名函数赋值给一个变量
* 也可以把匿名函数作为返回值

### 装饰器
* 在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）
* decorator接受一个函数并返回一个函数
*  @functools.wraps(func)　－将原始函数(func)的属性赋给新的函数


```markdown
f.__name__  //函数对象有一个__name__属性，可以拿到函数的名字("__"是两个"_")
```

* Python time time() 返回当前时间的时间戳

```markdown
import functools  //import functools是导入functools模块

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

### 偏函数
* functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单
* int2 = functools.partial(int, base=2)  －把int函数的base参数(做N进制的转换)固定为２(相当于加入kw = { 'base': 2 })
` 创建偏函数时，实际上可以接收函数对象、*args和**kw这3个参数 `

* max2 = functools.partial(max, 10)  －10会作为*args的一部分自动加到左边


