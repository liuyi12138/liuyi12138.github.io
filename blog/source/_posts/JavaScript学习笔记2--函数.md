---
title: JavaScript学习笔记2--函数
date: 2018-04-07 14:35:46
tags: JavaScript
categories: 编程语言
---

### js与c的比较学习
## 函数
<!--more--> 
#### 函数的定义与调用
* js中可将函数赋值给一个变量。 var abs=function(x){...}
* argumentsc只在函数内部起作用，并且永远指向传入的所有参数。arguments[i]
* arguments.length ——判断传入参数的个数
* rest函数:
```markdown
function exm(a,b,...rest){
console.log('a = '+ a);
console.log('b = '+ b);
console.log(rest);
}
```

#### 变量作用域与解构赋值
* JavaScript的函数在查找变量时从自身函数定义开始，从“内”向“外”查找。如果内部函数定义了与外部函数重名的变量，则内部函数的变量将“屏蔽”外部函数的变量。
* JavaScript会将所有申明的变量提升到函数顶部，所以尽量在函数顶部申明变量并赋值。
* JavaScript默认有一个全局变量window,声明的全局变量会绑定到window上，访问全局变量course和访问window.course等价
* 可把全局变量都绑定在唯一的名字空间，var MYAPP={};MYAPP.name='myapp';
* 解构赋值　var[x,y,z]=[1,2,3];
* 如果需要从一个对象中取出若干属性，也可以使用解构赋值，便于快速获取对象的指定属性
* 解构赋值简化代码：　[x,y]=[y,x]

#### 方法
* 在一个对象中绑定函数，称为这个对象的方法
* 在一个方法内部，this是一个特殊变量，它始终指向当前对象
* 用var that = this;，就可以放心地在方法内部定义其他函数，而不是把所有语句都堆到一个方法中。
* apply 在function.apply(对象，[参数])　——apply接收两个参数，一个是需要绑定的this变量，另一个是包含函数本身参数的数组
* apply与call的区别：apply()把参数打包成Array再传入；call()把参数按顺序传入。
```markdown
Math.max.apply(null, [3, 5, 4]);
Math.max.call(null, 3, 5, 4);
```

* 装饰器　apply()
```markdown
var count = 0;
var oldParseInt = parseInt; // 保存原函数
window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};
```

#### 高阶函数
** 接收另一个函数作为参数的函数叫做高阶函数　**
##### map/reduce
* arr.map(function)　——将arr中的每个参数依次传入function中
* map()传入的参数是函数对象本身
* reduce必须接收两个参数，并把结果和序列的下一个元素做累积计算

##### filter
* filter用于把Array中的某些元素过滤掉，filter也接收一个函数，然后把传入的函数依次作用于每个元素，根据返回值确定是否保留该元素
* filter()接收的回调函数，其实可以有多个参数。通常我们仅使用第一个参数，表示Array的某个元素。回调函数还可以接收另外两个参数，表示元素的位置和数组本身
```markdown
var arr = ['A', 'B', 'C'];
var r = arr.filter(function (element, index, self) {
    console.log(element); // 依次打印'A', 'B', 'C'
    console.log(index); // 依次打印0, 1, 2
    console.log(self); // self就是变量arr
    return true;
});
```

* 可以利用filter去掉重复元素
```markdown
r = arr.filter(function (element, index, self) {
    return self.indexOf(element) === index;
});
```

##### sort
* Array的sort()方法默认把所有元素先转换为String再排序，要自定义一个函数才能对数字进行大小排序
* sort()方法会直接对Array进行修改，它返回的结果仍是当前Array


#### 闭包
* 高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。
* 在函数function1中又定义了函数function2，并且，内部函数function2可以引用外部函数function1的参数和局部变量，当function1返回函数function2时，相关参数和变量都保存在返回的函数中。这种程序结构被称作闭包。
* 当我们调用function1时，每次调用都会返回一个新的函数。即使传入的参数相同，返回的函数也不同
* 返回闭包时牢记的一点就是：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

#### 箭头函数(Arrow Function)
* 箭头函数包含一条语句　x => x+x 相当于　function(x){return x+x;} 
* 箭头函数包含多条语句　x =>{... return ...;}
* 如果参数不是一个，就需要用括号()括起来　(x,y,...rest)
* x => ({ foo: x }) ——返回一个对象
* 箭头函数内部的this总是指向词法作用域，也就是外层调用者
* 用call()或者apply()调用箭头函数时，无法对this进行绑定，即传入的第一个参数被忽略

#### generator
* generator和函数不同的是，generator由function*定义（注意多出的*号），并且，除了return语句，还可以用yield返回多次。
```markdown
function* foo(x) {
    yield x + 1;
    yield x + 2;
    return x + 3;
}
```

* 1.function.next(); —— next()方法会执行generator的代码，然后，每次遇到yield x;就返回一个对象{value: x, done: true/false}，然后“暂停”。返回的value就是yield的返回值，done表示这个generator是否已经执行结束了。如果done为true，则value就是return的返回值。
* 2.直接用for ... of循环迭代generator对象，这种方式不需要我们自己判断done
```markdown
for(var x of function(10)){...}
```