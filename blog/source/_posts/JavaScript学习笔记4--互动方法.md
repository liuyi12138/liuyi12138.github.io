---
title: JavaScript学习笔记4--互动方法
date: 2018-04-07 15:51:35
tags: JavaScript
categories: 编程语言
---

### 慕课网实操学习–常用互动方法
<!--more--> 

* document.write() 可用于直接向 HTML 输出流写内容。简单的说就是直接在网页中输出内容
* alert(字符串或变量); ——alert弹出消息对话框(包含一个确定按钮)
* confirm(str);  ——confirm弹出对话框(包括一个确定按钮和一个取消按钮)
* prompt(str1, str2); ——prompt弹出消息对话框（包含一个确定按钮、取消按钮与一个文本输入框）
* window.open([URL], [窗口名称], [参数字符串])  ——open() 方法可以查找一个已经存在或者新建的浏览器窗口
* window.close();   //关闭本窗口  <窗口对象>.close();   //关闭指定的窗口

* 文档对象模型DOM（Document Object Model）定义访问和处理HTML文档的标准方法。DOM 将HTML文档呈现为带有元素、属性和文本的树结   构（节点树）
![12.25.1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS9tYXJrZG93bi4zLmpwZw?x-oss-process=image/format,png)

* document.getElementById(“id”) ——获取的元素是一个对象，如想对元素进行操作，我们要通过它的属性或方法
* Object.innerHTML  ——innerHTML 属性用于获取或替换 HTML 元素的内容
* Object.style.property=new style;
* Object.style.display = value; ——none(隐藏) block(显示)
* object.className = classname ——className 属性设置或返回元素的class 属性