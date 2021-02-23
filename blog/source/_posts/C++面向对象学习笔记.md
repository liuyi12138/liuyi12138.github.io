---
title: C++面向对象学习笔记
date: 2018-05-14 20:34:04
tags: C++
categories: 编程语言
---

## C++和C的比较学习
<!--more--> 

### 枚举(关键字 enum)
* 默认情况下，第一个名称值为0，后面依次+1，给特定名称赋值后，前面的名称值不变，后面的依次变化
```
    enum color { red, green=5, blue }c;
    c = blue;
```
### C++类
* 类成员的访问类型：
    * public:公有类型成员
     * private:私有类型成员
     * protected:保护类型成员
```
class Box
{
   public:
      double length;   // 盒子的长度
      double breadth;  // 盒子的宽度
      double height;   // 盒子的高度
};
```

### C++继承
* 派生类
    * 命名形式` class derived-class: access-specifier base-class `
    * derived-class:派生类名，access-specifier：类成员的访问类型，base-class：基类
    * 基类中访问类型为private的成员不可被派生类访问(protected可以)
* 继承类型
    * 公有继承（public）：当一个类派生自公有基类时，基类的公有成员也是派生类的公有成员，基类的保护成员也是派生类的保护成员，基类的私有成员不能直接被派生类访问，但是可以通过调用基类的公有和保护成员来访问
    * 保护继承（protected）： 当一个类派生自保护基类时，基类的公有和保护成员将成为派生类的保护成员
    * 私有继承（private）：当一个类派生自私有基类时，基类的公有和保护成员将成为派生类的私有成员
* 多继承
    * 命名形式`class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…`

### C++多态
* 如果父类与子类中含有相同的函数名，子类继承时该函数被父类的函数覆盖
* 在父类的函数声明前放置关键字 virtual则优先执行子类中的函数
* 在基类中使用关键字 virtual 声明的函数称为虚函数，在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数