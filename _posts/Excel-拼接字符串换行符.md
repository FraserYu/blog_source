---
title: Excel 拼接字符串换行符
tags:
  - Excel
  - 换行符
  - 公式
categories: 办公
abbrlink: cd3eeee9
date: 2016-10-11 15:52:08
---

### Excel通过公式拼接字符串换行
我们通常都会通过Excel公式**=CONCATENATE(Text1,[Text2])**来拼接字符串，当我们需要拼接的字符串中需要换行时我们可以使用**CHAR(10)**来完成拼接换行符，请看如下例子

	=CONCATENATE("hello",CHAR(10),"world")

输出结果如下：

    hello
    world

**NOTE:** 使用拼接字符串公式的单元格请设置为**常规**，不要是自动换行.
