---
title: C++智能指针简介
date: 2018-07-23 23:09:26
tags: C++
categories: 编程语言
---

## 一.使用背景
由于 C++ 语言没有自动内存回收机制，程序员每次 new 出来的内存都要手动 delete，比如流程太复杂，最终导致没有 delete，异常导致程序过早退出，没有执行 delete 的情况并不罕见，并造成内存泄露。因此C++ 引用了智能指针，智能指针即是C++RAII的一种应用，可用于动态资源管理，资源即对象的管理策略。

**智能指针在`<memory>`标头文件的 std 命名空间中定义。**

<!--more--> 

### 智能指针的优点
* 1）智能指针能够帮助我们处理资源泄露问题；

* 2）它也能够帮我们处理空悬指针的问题；

* 3）它还能够帮我们处理比较隐晦的由异常造成的资源泄露。

## 二.各类智能指针用法简介
**C++ 智能指针主要包括：unique_ptr,shared_ptr, weak_ptr, 这三种(auto_ptr 已被遗弃)**

### 1.shared_ptr的使用
#### 1)shared_ptr多个指针指向相同的对象
shared_ptr使用引用计数，每一个shared_ptr的拷贝都指向相同的内存，每使用他一次，内部的引用计数加1，每析构一次，内部的引用计数减1，减为0时，自动删除所指向的堆内存。
#### 2)创建方式
shared_ptr的创建,有两种方式
```c++
shared_ptr<int> p1 = make_shared<int>(1);// 通过make_shared函数
shared_ptr<int> p2(new int(2));// 通过原生指针构造
```
#### 3)注意事项
* 智能指针是一个类不是指针，不能将指针直接赋值给一个智能指针 
    * 例如std::shared_ptr<int> p4 = new int(1);的写法是错误的
* 拷贝使得对象的引用计数增加1，赋值使得原对象引用计数减1,后来指向的对象引用计数加1
* 不要用一个原始指针初始化多个shared_ptr，否则会造成二次释放同一内存
* [循环引用问题](#jump1)

#### 4)代码示例
```c++
#include <iostream>
#include <memory>

int main() {
    {
        int a = 1;
        std::shared_ptr<int> ptra = std::make_shared<int>(a);
        std::shared_ptr<int> ptra2(ptra); //拷贝，引用计数+1
        std::cout << ptra.use_count() << std::endl; //2

        int b = 2;
        int *pb = &a;
        //std::shared_ptr<int> ptrb = pb;  //将指针赋值给一个智能指针 error
        std::shared_ptr<int> ptrb = std::make_shared<int>(b);
        ptra2 = ptrb; //赋值，引用计数-1
        pb = ptrb.get(); //获取原始指针

        std::cout << ptra.use_count() << std::endl; //2
        std::cout << ptrb.use_count() << std::endl; //1
    }
    //超出作用域，内存释放
}
```
### 2.unique_ptr的使用
#### 1）unique_ptr同一时刻只能有一个unique_ptr指向给定对象
unique_ptr禁止拷贝语义、只能通过移动语义转移所有权

#### 2)创建方式
unique_ptr的创建,与shared_ptr相似，也是两种方式
```c++
shared_ptr<int> p1 = make_unique<int>(1);
std::unique_ptr<int> uptr(new int(2));
```

#### 3)注意事项
* 通过reset方法重新指定
* 通过移动语义转移所有权
* 通过release方法释放所有权

#### 4)代码示例
```c++
#include <iostream>
#include <memory>

int main() {
    {
        std::unique_ptr<int> uptr(new int(10));  //绑定动态对象
        //std::unique_ptr<int> uptr2 = uptr;  //不能赋值 error
        //std::unique_ptr<int> uptr2(uptr);  //不能拷贝 error
        std::unique_ptr<int> uptr2 = std::move(uptr); //转移所有权
        uptr2.reset(new int(20));  //重新指定对象
        uptr2.release(); //释放所有权
    }
    //超出uptr的作用域，内存释放
}
```

### 2.weak_ptr的使用
#### 1）weak_ptr是为了配合shared_ptr而引入的一种智能指针
weak_ptr的最大作用在于协助shared_ptr工作，像旁观者那样观测资源的使用情况

#### 2)创建方式
weak_ptr可以从一个shared_ptr或者另一个weak_ptr对象构造，获得资源的观测权
```c++
std::weak_ptr<int> wp1(sh_ptr); //通过shared_ptr构造
std::weak_ptr<int> wp2(wp1);/   //通过另一个weak_ptr构造
```
#### 3)注意事项
* weak_ptr可以使用成员函数lock()从被观测的shared_ptr获得一个可用的shared_ptr对象
* weak_ptr的成员函数expired()用来观测资源的引用计数是否为0,若use_count()==0,则expired()==true

#### 4)代码示例
```c++
#include <iostream>
#include <memory>

int main() {
    {
        std::shared_ptr<int> sh_ptr = std::make_shared<int>(1);
        std::cout << sh_ptr.use_count() << std::endl;//1

        std::weak_ptr<int> wp(sh_ptr);
        std::cout << wp.use_count() << std::endl; //1,构造weak_ptr不增加引用计数

        if(!wp.expired()){
            std::shared_ptr<int> sh_ptr2 = wp.lock(); //使用成员函数lock()
            *sh_ptr2 = 100;
            std::cout << wp.use_count() << std::endl; //2
        }
    }
    //超出作用域，内存释放
}
```

## <span id = "jump1">三.循环引用问题</span>
### 1.循环引用代码示例
```c++
class B;
class A
{
public:
　　shared_ptr<B> m_b;
};
 
class B
{
public:
　　shared_ptr<A> m_a;
};
 
int main()
{
    {
　　　　shared_ptr<A> a(new A); //new出来的A的引用计数此时为1
　　　　shared_ptr<B> b(new B); //new出来的B的引用计数此时为1
　　　　a->m_b = b; //B的引用计数增加为2
　　　　b->m_a = a; //A的引用计数增加为2
　　}
 
　　//b先出作用域，B的引用计数减少为1，不为0，所以堆上的B空间没有被释放，且B持有的A也没有机会被析构，A的引用计数也完全没减少
 
　　//a后出作用域，同理A的引用计数减少为1，不为0，所以堆上A的空间也没有被释放
 
    //a和b互相抓住对方的引用不放，导致内存泄漏
}
```

### 2.解除循环引用的方法
一般来讲，解除这种循环引用有下面有三种可行的方法(参考)：
* 1)当只剩下最后一个引用的时候需要手动打破循环引用释放对象
* 2)当A的生存期超过B的生存期的时候，B改为使用一个普通指针指向A
* 3)使用弱引用的智能指针打破这种循环引用

但方法1和方法2都需要程序员手动控制，麻烦且容易出错,所以我们一般使用第三种方法：弱引用的智能指针weak_ptr

**对于示例中的代码，只需要将`shared_ptr<B> m_b`改为`weak_ptr<B> m_b`,则B的引用计数不会增加为2,B出作用域后可以正常释放,A也就能正常释放了**

## 三.shared_ptr智能指针类的简单实现
### 1.智能指针的原理
* 1)创建新对象时，初始化指针，并设置引用计数为1
* 2)当对象作为另外一个对象的副本创建，也就是调用拷贝构造函数时，拷贝指针，并且，增加引用计数
* 3)当对一个对象进行赋值时，左操作数所指对象的引用计数减少，如果减少为0，则删除对象，右操作数所指对象的引用计数增加
* 4)调用析构函数，减少引用计数，如果减至0，则删除指针

### 2.智能指针的实现
引用计数的实现由两种经典策略：引入辅助类和使用句柄。
#### 1)辅助类实现引用计数
* U_Ptr作为辅助类,封装实际的指针对象和引用计数值
* HasPtr作为对外使用的类，构造时传入实际的指针对象
* HasPtr内部包含了一个辅助类U_Ptr的指针对象，多个HasPtr类对象指向同一个U_Ptr对象
* U_Ptr依靠引用计数来实现实际指针对象的释放

```c++
//模板类作为友元类，要事先声明
template <class T> 
class HasPtr;
 
//辅助类
template<typename T>
class U_Ptr {
	friend class HasPtr<T>;	//友元类
	T *ip;	//实际指针对象
	size_t use;	//引用计数
	U_Ptr(T *p)
            :ip(p), use(1)
    { }//构造函数
	~U_Ptr() //析构函数
	{
		delete ip;
	}
};
 
template<typename T>
class HasPtr 
{
public:
	// 构造函数，引用计数初始化为1
	explicit HasPtr(T *p)
		:ptr(new U_Ptr<T>(p))
	{ }
 
	// 拷贝构造函数，引用计数加1
	HasPtr(const HasPtr<T> &orig)
		:ptr(orig.ptr)
	{
		++ptr->use;
	}
 
	//赋值
	HasPtr<T>& operator=(const HasPtr<T>& rhs)
	{
		++rhs.ptr->use;     // 操作符右值自加
		if (--ptr->use == 0)	//左值自减，并判断是否减至0
			delete ptr;    // 减至0则删除
		ptr = rhs.ptr;      // 拷贝指针
		return *this;
 
	}
 
	// 析构，自减
	~HasPtr()
	{ 
		if (--ptr->use == 0)
			delete ptr;
	}
 
    //重载箭头运算符
	T* operator->()
	{
		if(ptr)
			return ptr;
		throw std::runtime_error("access through NULL pointer");
	}
 
	const T* operator->() const
	{
		if(ptr)
			return ptr;
		throw std::runtime_error("access through NULL pointer");
	}
 
    //重载解引用运算符
	T& operator*()
	{
		if(ptr)
			return *ptr;
		throw std::runtime_error("dereference of NULL pointer");
	}
 
	const T& operator*() const
	{
		if(ptr)
			return *ptr;
		throw std::runtime_error("dereference of NULL pointer");
	}
 
private:
	U_Ptr<T> *ptr;        // 辅助类对象
};
```

#### 2)句柄类实现引用计数
**不用引入辅助类，可以直接把指针封装起来。然后，重载操作符，定义为一个指针的行为**
* 1)定义一个SmartPtr对象P1，传入实际指针对象，调用构造函数，初始化计数为1
* 2)定义一个SmartPtr对象P2，调用拷贝构造函数，此时，P1和P2的ptr指向相同的地址，pUse指向相同的地址，引用计数自加
* 3)定义一个SmartPtr对象P3，调用赋值，操作符右操作数引用计数自加，左操作数自减，并判断原引用计数是否为0，如果是0，则删除原ptr指针指向的地址内容，赋值ptr和pUse，指向相同的ptr和pUse
* 4)析构时，引用计数自减，并判断计数值是否为0，如果是0，则自动删除指针对象

```c++
template<typename T>
class SmartPtr
{
public:
	SmartPtr(T* p= 0)  //构造函数
		:ptr(p),pUse(new size_t(1))
	{}
 
	~SmartPtr() //析构函数
	{
		decrUse();
	}
 
    //拷贝构造函数，引用计数加1
	SmartPtr(const SmartPtr<T>& src)
		:ptr(src.ptr),pUse(src.pUse)
	{
		++*pUse;
	}
 
    //赋值
	SmartPtr<T>& operator=(const SmartPtr<T>& rhs)
	{
		if (rhs.ptr != ptr)
		{
			++*rhs.pUse;
			decrUse();
			ptr = rhs.ptr;
			pUse = rhs.pUse;
		}
		return *this;
	}

    //重载箭头运算符
	T* operator->()
	{
		if(ptr)
			return ptr;
		throw std::runtime_error("access through NULL pointer");
	}
 
	const T* operator->() const
	{
		if(ptr)
			return ptr;
		throw std::runtime_error("access through NULL pointer");
	}
 
    //重载解引用运算符
	T& operator*()
	{
		if(ptr)
			return *ptr;
		throw std::runtime_error("dereference of NULL pointer");
	}
 
	const T& operator*() const
	{
		if(ptr)
			return *ptr;
		throw std::runtime_error("dereference of NULL pointer");
	}
 
private:
 
	void decrUse()
	{
		if(--*pUse == 0)
		{
			delete ptr;
			delete pUse;
		}
	}
 
private:
	T* ptr;
	size_t* pUse;
};
```