---
title: markdown学习笔记
date: 2018-04-07 15:53:03
tags: markdown
categories: 工具使用
---
## markdown 基本用法
<!--more--> 

### 1.标题
在 Markdown 中，如果一段文字被定义为标题，只要在这段文字前加 # 号即可。
有多少个#即为几级标题。

![markdown.1.jpg](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS9tYXJrZG93bi4xLmpwZw?x-oss-process=image/format,png)

**TIPS : 在Git中 ‘#’ 后需加上空格**

### 2.列表
在 Markdown 中，列表的显示只需要在文字前加上 - 或 * 即可变为无序列表，
有序列表则直接在文字前加1. 2. 3. 符号要和文字之间加上一个字符的空格。

  - 1.
  * 2.
  - 3


![markdown.2.jpg](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS9tYXJrZG93bi4yLmpwZw?x-oss-process=image/format,png)

**Tips ： ‘-‘和’*‘在同一个无序列表可以混用**
**TIPS ： 空一格风味更佳哦**

  - 1.
   - 2.
   - 3.

### 3.引用
在Markdown中，在一段文字前加上’>'即将这一段话定义为引用。


> 这是一段引用


**TIPS ： 注意’>'与文字之间应有空格**

### 4.图片与链接

```markdown
![我是图片](http://)

[我是链接](http://)

```

**TIPS ： 插入图片需要图床哦，不要天真得以为加上本地链接就可以了**

### 5.强调

可以使用**加粗**和*斜体*来格式化文本内容。

```markdown
加粗使用: **这种语法**
斜体使用: *这种语法* 或 _这种语法_
还可以混合使用：**加粗文本中的_斜体_**
```

### 6.高亮代码
在两组 ```之间的文本被识别为代码
两个 ` 之间被识别为行内代码

**TIPS : 在第一组```后面加上某些字符会有特效哦 比如 cpp Python 而且加上markdown是万能的呢**

```cpp
 # include<stdio.h>
 int main()
 {
	printf("hello,world!);
    return 0;
 }
```

### 7.表格

|               | Title 1(居中对齐)  | Title 2 （右对齐） |  Title 3 （左对齐）  |
| ------------- |:-----------------:|-----------------:|:--------------------|
| 	Tip 1       |    1.1            |      1.2         |    1.3              |
|   Tip 2       |    1.2            |   2.2            |    2.3              |
|   Tip 3       |    1.3            |   3.2            |    3.3              |

**TIPS ：你还可以使用 : 指定列的对齐方式。**

```markdown
|               | Title 1(居中对齐)  | Title 2 （右对齐） |  Title 3 （左对齐）  |
| ------------- |:-----------------:|-----------------:|:--------------------|
| 	Tip 1       |    1.1            |      1.2         |    1.3              |
|   Tip 2       |    1.2            |   2.2            |    2.3              |
|   Tip 3       |    1.3            |   3.2            |    3.3              |
```

### 8.图表

[**图表制作参考链接**](https://www.zybuluo.com/mdeditor)


_ _ _

本文参考链接
[**参考链接**](http://www.jianshu.com/p/1e402922ee32/)