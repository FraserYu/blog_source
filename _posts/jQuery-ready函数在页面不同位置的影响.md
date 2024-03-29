---
title: jQuery ready函数在页面不同位置的影响
tags: jQuery
category: 前端
abbrlink: f2d66a8b
date: 2016-03-30 11:28:48
---
## jQuery ready 函数在页面不同位置与浏览器的兼容性调查
-----------------------------------------------

### 抛出问题
最近修改历史代码发现这样的一个问题， 一段jQuery函数在FF,Chrome,IE10,11 都可以执行，唯独在IE9中不能执行，IE9以下的没有测试过

### 代码分析排查
非常简单的一段jQuery代码，用来改变页面元素的样式，大概如下

```javascript
	$(document).ready(function() {
		......
	});
```

<!-- more -->

1. 判断函数体中的内容是否在书写方式产生了浏览器的不兼容问题
> 直接在代码中写一个alert（‘--’）；alert也不执行，所以排除掉是代码书写方式的问题

2. 判断这段代码位置是否有影响
> 首先历史代码是写在<\/html> 结束标签之后，于是移动位置到<\/html>里面，发现代码生效
> 这样发现，IE9是不能解析在html标签后面的jQuery代码的

**总结：** 书写代码要以标准的形式来写，最好不要以inline的形式来写，将JavaScript代码提出到一个js文件中，应在head中引入。
