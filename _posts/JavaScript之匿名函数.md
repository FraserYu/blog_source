---
title: JavaScript之匿名函数
tags: 'js,匿名函数'
category: 前端
abbrlink: d203a44f
date: 2016-02-29 18:52:22
---
# JavaScript之匿名函数
-----------------------------------------------
### JavaScript定义函数的三种方式
函数在JavaScript中是最灵活也最普遍的应用，主要有三种定义方式：
1. 最常见的一种：

		function double(x){    
		    return 2 * x;       
		}
2. 使用了Function构造函数，把参数列表和函数体都作为字符串，很不方便，不建议使用:
<!-- more -->
		var double = new Function('x', 'return 2 * x;');
3. 匿名函数:

		var double = function(x) { return 2* x; }

### 自执行的匿名函数
这里要引用在 Stack Overflow中看到的解释：
> Lets explain by Breaking up the code
>
	function () {}()
> Or often written as
>
	(function () {})()
Is a **self-invoking** anonymous function, also known as **Immediately-Invoked** Func­tion Expres­sions (IIFEs). Which executes the anonymous function inline immediately.

> Read more about this at [Explain JavaScript's encapsulated anonymous function syntax.](http://stackoverflow.com/questions/1634268/explain-javascripts-encapsulated-anonymous-function-syntax)
> Anonymous functions are a powerful feature and have benefits like scoping ("variable name spacing"), see [What is the purpose of a self executing function in javascript?](http://stackoverflow.com/questions/592396/what-is-the-purpose-of-a-self-executing-function-in-javascript)

> Now they are using
>
	!function ($) {}(window.jQuery)
Let's skip the ! for now.
So they are passing, window.jQuery into that function as argument and accepting as \$.
What this does is making \$ an alias to **window.jQuery** (original jQuery Object) and hence ensuring that the \$ will always refer to the jQuery object inside that closure, no matter if other library has taken that(\$) outside.So code you write inside that closure using \$ will always work.

> Another benefit is that \$ comes as an argument in the anonymous function, which brings it closer in the scope chain and hence it takes less time for the JS interpreter to find the \$ object inside the closure than it would otherwise took if we used the global \$.
>
	$(function(){ })
It's jQuery's document ready block as you might already know, which ensures that code inside this function will run when **dom is ready**, and hence all event binding's will work properly. Read more at http://api.jquery.com/ready/

> And what that ! does has been well explained here or at What does the exclamation mark do before the function?
**In Short:**
> To demonstrate the benefits of !, Lets consider a case,
>
	  (function() {
	 alert('first');
	}())
> 	
	(function() {
	    alert('second');
	}())
If you paste the above code in console, you will get two alerts, but then you will get this error
**TypeError: undefined is not a function**
>
> Why this happens? Let's simulate how JS engines executes the above code block. It executes this anonymous function function() {alert('first');}() shows the alert and as it returns nothing undefined is returned inside the (). Same happens for the second function too. So after the execution of this block, it ends up having something like **(undefined)(undefined)** and as it's syntax is like a **self-invoking anonymous function**, it tries to call that function, but the first, (undefined) is not a function. So you get undefined is not a function error. ! fixes this kind or errors. What happens with !. I am quoting the lines from the above answer link.

> When you use !, the function becomes the single operand of the unary (logical) NOT operator. This forces the function to be evaluated as an expression, which allows it to be invoked immediately inline. and this solves the above problem, we can rewrite the above block using ! like
>
	!(function() {
	    alert('first');
	}())
	!(function() {
	    alert('second');
	}())
For your case you can simply put your tooltip code inside a document ready block like this, of course, you can also use void or ~ syntax.
