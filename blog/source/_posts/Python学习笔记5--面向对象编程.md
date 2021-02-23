---
title: Python学习笔记5--面向对象编程
date: 2018-04-08 17:53:26
tags: Python
categories: 编程语言
---

###  python与javascript的比较学习

## 面向对象编程(OOP)
<!--more--> 

### 类和实例
* class Student(object):   －Student是类名，object表示该类是从哪个类继承下来的(如果没有合适的继承类，就使用object类，这是所有类最终都会继承的类)
* 通过定义`__init__`方法绑定属性(创建实例的时不能传入空的参数必须传入与`__init__`方法匹配的参数,但self不需要传)

```markdown
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score
```

### 访问限制
* 实例的变量名如果以`__`开头，就变成了一个私有变量,，只有内部可以访问，外部不能访问
* Python解释器对外把`__name`变量改成了`_Student__name`，所以，仍然可以通过`_Student__name`来访问`__name`变量

### 继承和多态
* 子类获得了父类的全部功能
* 当子类和父类存在相同的方法时，子类的方法覆盖父类的方法
* 多态对扩展开放，对修改封闭

### 获取对象信息
* 可以使用isinstance()函数判断class类型
* 如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list
* 测试该对象的属性
```markdown
>>> hasattr(obj, 'x') # 有属性'x'吗？
True
>>> obj.x
9
>>> hasattr(obj, 'y') # 有属性'y'吗？
False
>>> setattr(obj, 'y', 19) # 设置一个属性'y'
>>> hasattr(obj, 'y') # 有属性'y'吗？
True
>>> getattr(obj, 'y') # 获取属性'y'
19
>>> obj.y # 获取属性'y'
19
>>> getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
404
```

***