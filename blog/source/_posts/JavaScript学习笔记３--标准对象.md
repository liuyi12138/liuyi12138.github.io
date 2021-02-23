---
title: JavaScript学习笔记３--标准对象
date: 2018-04-07 14:40:10
tags: JavaScript
categories: 编程语言
---

### js与c的比较学习

##  标准对象

<!--more--> 

#### Date
* Date对象用来表示日期和时间。
```markdown
var now = new Date();
now; // Wed Jun 24 2015 19:49:22 GMT+0800 (CST)
now.getFullYear(); // 2015, 年份
now.getMonth(); // 5, 月份，注意月份范围是0~11，5表示六月
now.getDate(); // 24, 表示24号
now.getDay(); // 3, 表示星期三
now.getHours(); // 19, 24小时制
now.getMinutes(); // 49, 分钟
now.getSeconds(); // 22, 秒
now.getMilliseconds(); // 875, 毫秒数
now.getTime(); // 1435146562875, 以number形式表示的时间戳
```

* var d = new Date(2015, 5, 19, 20, 15, 30, 123); ——创建一个指定日期和时间的Date对象
** JavaScript的Date对象月份值从0开始，牢记0=1月，1=2月，2=3月，……，11=12月**

* var d = Date.parse('2015-06-24T19:49:22.875+08:00'); ——解析一个符合ISO 8601格式的字符串，但它返回的不是Date对象，而是一个时间戳
* var d = new Date(1435146562875);　——把时间戳转换为Date对象

**使用Date.parse()时传入的字符串使用实际月份1-12，转换为Date对象后getMonth()获取的月份值为0-11。**

#### RegExp

* 在正则表达式中，用*表示任意个字符（包括0个），用+表示至少一个字符，用?表示0个或1个字符，用{n}表示n个字符，用{n,m}表示n-m个字符

* '-'是特殊字符，在正则表达式中，要用'\'转义
* \s匹配空格
* **要做更精确地匹配，可以用[]表示范围**

![正则表达式进阶](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yNC4xLnBuZw?x-oss-process=image/format,png)

* 创建一个正则表达式
```markdown
var re1 = /ABC\-001/;
var re2 = new RegExp('ABC\\-001');
```
** 如果使用第二种写法，因为字符串的转义问题，字符串的两个\\实际上是一个\ **

* re.test('....')；　 ——RegExp对象的test()方法用于测试给定的字符串是否符合条件
* 'a,b;; c  d'.split(/[\s\,\;]+/); // ['a', 'b', 'c', 'd']　——切分字符串
* 分组——除了简单地判断是否匹配之外，正则表达式还有提取子串的强大功能。用()表示的就是要提取的分组（Group）
```markdown
var re = /^(\d{3})-(\d{3,8})$/;
re.exec('010-12345'); // ['010-12345', '010', '12345']　——在RegExp对象上用exec()方法提取出子串来。
```

* exec()方法在匹配成功后，会返回一个Array，第一个元素是正则表达式匹配到的整个字符串，后面的字符串表示匹配成功的子串
* 正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符
* 加个?就可以让\d+采用非贪婪匹配
* 全局搜索
```markdown
var r1 = /test/g;
// 等价于:
var r2 = new RegExp('test', 'g');
```

* 全局匹配可以多次执行exec()方法来搜索一个匹配的字符串。当我们指定g标志后，每次运行exec()，正则表达式本身会更新lastIndex属性，表示上次匹配到的最后索引
* 全局匹配类似搜索，因此不能使用/^...$/，那样只会最多匹配一次
* 正则表达式还可以指定i标志，表示忽略大小写，m标志，表示执行多行匹配


#### JSON

**JSON中的数据类型**

* number：和JavaScript的number完全一致
* boolean：就是JavaScript的true或false
* string：就是JavaScript的string
* null：就是JavaScript的null
* array：就是JavaScript的Array表示方式——[]
* object：就是JavaScript的{ ... }表示方式

* JSON的字符串规定必须用双引号""，Object的键也必须用双引号""
* var s = JSON.stringify(xiaoming); ——把小明这个对象序列化成JSON格式的字符串
* JSON可以加上参数，第一个参数是对象，第二个参数用于控制如何筛选对象的键值，还可以传入一个函数
* JSON.parse('[1,2,3]') ——反序列化，把一个JSON格式的字符串变成一个JavaScript对象