---
title: CSS pseudo
tags: 伪元素
category: 前端
abbrlink: fec28ce1
date: 2016-02-17 16:33:36
---
 CSS之伪元素
-----------------------------------------------


### 前言
最近做项目用到了CSS伪元素:before 和 :after, 理解的不是很好，于是乎通过查阅资料进行实践、记录、整理，非常感谢原作者的奉献，参考放在了文章末尾处。

### 什么是伪元素
伪元素实际上在CSS1中就存在了，但是我们现在所讨论的:before和:after则发布于CSS2.1中。在最初，伪元素的语法是使用“:”（一个冒号），随着web的发展，在CSS3中修订后的伪元素使用“::”（两个冒号），也就是::before 和 ::after—以区分伪元素和伪类（比如:hover，:active等），这里主要说明:before 和 :after。

<!-- more -->

### 什么是:before和:after？ 该如何使用他们？
> 1. :before是css中的一种伪元素，可用于在某个元素之前插入某些内容。
> 2. :after是css中的一种伪元素，可用于在某个元素之后插入某些内容。

下面来看个例子

	<!DOCTYPE html>
		<head>
		</head>
		<body>
			<style>
				p:before{
					content: "H";
				}
				p:after{
					content: "d";
				}
			</style>
			<p>ello Worl</p>
		</body>
	</html>

在页面中显示的就是Hello World. 我们通过浏览器审查元素看到如下的结果：

	<p>
		::before
		"ello Worl"
		::after
	</p>

p标签内部的内容的前面会被插入一个:before伪元素，该伪元素内包含的内容是”H”；而在p标签内的内容后面会被插入一个:after伪元素，该元素包含的内容是”d”。

### 深入探讨
通过几个例子来深入探讨说明伪元素的强大之处.

#### 结合border写个对话框的样式
在实现这个功能之前我们先来看看如何用border来实现三角形，向html文件中增加如下元素：

	<style>
			.triangle{
				width: 0;
				height: 0;
				border-left:50px solid red;
				border-bottom:50px solid blue;
				border-top:50px solid black;
				border-right:50px solid purple
			}
	</style>
	<div class="triangle"></div>

以上代码将会在页面上展示一个正方形，左边是个红色的三角形，右边是紫色的三角形，上面是黑色的三角形，下面是蓝色的三角形，我们来看一下效果：
![Triangle](http://7xkyc7.com1.z0.glb.clouddn.com/blog_css_pseudo_triangle.png)

那么我们如何来得到这个三角形呢？

	<style>
		.triangle{
		      width: 0;
		      height: 0;
		      border:50px transparent solid;  /*这里我们将元素的边框宽度设置为50px，transparent表示边框颜色是透明的，solid表示边框是实线的*/
		      border-top-color: black;   /*这里我们仅将上边框的颜色设置为黑色，众所周知，css后面的样式代码会覆盖之前的相同的样式代码，至于其他三边的还是透明色*/
		      //border-bottom-color: black;        /*这里设置底部边框色为黑色*/
		      border-left-color: black;          /*这里设置左边边框色为黑色*/
		      //border-right-color:black           /*这里设置右边边框色为黑色*/
		  }
	</style>

我们把所有边框都设置成透明，然后把某一个边框加上颜色就是三角形了：
![Triangle1](http://7xkyc7.com1.z0.glb.clouddn.com/blog_css_pseudo_triangle1.png)

到此，我们修改样式：

	<style>
	    .test-div{
	        position: relative;  /*日常相对定位*/
	        width:150px;
	        height:36px;
	        border-radius:5px;
	        border:black 1px solid;
	        background: rgba(245,245,245,1)
	    }
	    .test-div:before{
	        content: "";  
	        display: block;
	        position: absolute;  /*日常绝对定位*/
	        top:8px;
	        width: 0;
	        height: 0;
	        border:6px transparent solid;
	        left:-12px;
	        border-right-color: rgba(245,245,245,1);
	    }
	  </style>
	  <div class="test-div"></div>

![Dialog1](http://7xkyc7.com1.z0.glb.clouddn.com/blog_css_pseudo_dialog1.png)

仔细瞧见，我们发现三角形没有黑色的边框啊，我们继续修改代码通过:after 来实现：

	<style>
	    .test-div{
	        position: relative;  /*日常相对定位*/
	        width:150px;
	        height: 36px;
	        border:black 1px solid;
	        border-radius:5px;
	        background: rgba(245,245,245,1)
	    }
	    .test-div:before,.test-div:after{
	        content: "";  
	        display: block;
	        position: absolute;  /*日常绝对定位*/
	        top:8px;
	        width: 0;
	        height: 0;
	        border:6px transparent solid;
	    }
	    .test-div:before{
	        left:-11px;
	        border-right-color: rgba(245,245,245,1);
	        z-index:1
	    }
	    .test-div:after{
	        left:-12px;
	        border-right-color: rgba(0,0,0,1);
	        z-index: 0
	    }
	 </style>
	 <div class="test-div"></div>
注意在:before中left是-11px, z-index是1；:after中left是-12px, z-index：0，相差一个像素，并且层上有叠加，才看到那个黑色的三角边框，如下：
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/blog_css_pseudo_dialog2.png)

#### 半透明登录背景层
现在看到好多登录框都有一个半透明的背景层，让人看起来很舒服：

	<style>
	      body{
	          background: red  /*这里本兽加了个红色背景，用以区分背景的半透明及内容的完全不透明*/
	      }
	      .test-div{
	          position: relative;  /*日常相对定位(重要，下面内容也会介绍)*/
	          width:300px;
	          height: 120px;
	          padding: 20px 10px;
	          font-weight: bold;
	      }
	      .test-div:before{
	          position: absolute;  /*日常绝对定位(重要，下面内容也会略带介绍)*/
	          content: "";  
	          top:0;
	          left: 0;
	          width: 100%;  /*和内容一样的宽度*/
	          height: 100%;  /*和内容一样的高度*/
	          background: rgba(255,255,255,.5); /*给定背景白色，透明度50%*/
	          z-index:-1 /*日常元素堆叠顺序(重要，下面内容也会略带介绍)*/
	      }
	  </style>

	  <div class="test-div">
	      <table>
	          <tr>
	              <td>Name</td>
	              <td><input placeholder="your name" /></td>
	          </tr>
	          <tr>
	              <td>Password</td>
	              <td><input placeholder="your password" /></td>
	          </tr>
	          <tr>
	              <td></td>
	              <td><input type="button" value="login" /></td>
	          </tr>
	      </table>
	  </div>

我们来看一下效果：
![login](http://7xkyc7.com1.z0.glb.clouddn.com/blog_css_pseudo_login.png)

### 其他相关CSS属性介绍
上面虽然主要讲:before 和 :after， 但是也用到了很多其他的CSS属性

#### position
> position属性规定了元素的定位类型，默认为static, 该属性还可以有下值：
> 1. absolute：生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。
> 2. fixed：生成绝对定位的元素，相对于浏览器窗口进行定位。
> 3. relative：生成相对定位的元素，相对于其正常位置进行定位。
> 4. inherit：规定应该从父元素继承 position 属性的值。

	<!--position:absolute-->
	<style>
	      body{
	          height: 2000px  /*这里将body的高度设置为2000px是为了区分absolute和fixed的差别*/
	      }
	      .test-div{
	          position:absolute;
	          left:50px;
	          top:50px
	      }
	  </style>
	  <div class="test-div">Hello World</div>
	<!--position:fixed-->
	  <style>
	      body{
	          height: 2000px  /*这里将body的高度设置为2000px是为了区分absolute和fixed的差别*/
	      }
	      .test-div{
	          position:fixed;
	          left:50px;
	          top:50px
	      }
	  </style>
	  <div class="test-div">Hello World</div>
	<!--position:relative + position:absolute-->
	  <style>
	      .out-div{
	          width: 300px;
	          height: 300px;
	          background: purple;  /*这里定义个背景，让我们知道这个div在哪*/
	          margin:50px 0px 0px 50px;
	          position: relative
	      }
	      .in-div{
	          position:absolute;
	          left:50px;
	          top:50px
	      }
	  </style>
	  <div class="out-div">
	      <div class="in-div">Hello World</div>
	  </div>

具体效果请自己来实践。

#### z-index 元素堆叠排序
> z-index用于设置或检索对象的堆叠顺序，对应的脚本特性为z-Index。
> z-index的数值越大，该元素的堆叠层级越高。

	<style>
	      .first-div{
	          width: 300px;
	          height: 300px;
	          background: purple;  /*这里定义个背景，让我们知道这个div在哪*/
	          position: absolute;
	          left:50px;
	          top:50px;
	          z-index: 1
	      }
	      .second-div{
	          position:absolute;
	          left:80px;
	          top:80px;
	          width:50px;
	          height: 50px;
	          background: white;
	          z-index: 2
	      }
	  </style>
	  <div class="first-div"></div>
	  <div class="second-div"></div>

具体效果请自己实践。

#### zoom 元素缩放比例
zoom适用于所有元素，用于设置或检索对象的缩放比例，对应的脚本特性为zoom，原比例的值是1，请到W3School中查看具体特性，支持浮点型值，注意浏览器兼容问题。

	<style>
	      div{
	          width: 100px;
	          height: 100px;
	          float: left
	      }
	      .first-div{
	          background: purple;
	          zoom:1.5
	      }
	      .second-div{
	          background: black;
	          zoom:1
	      }
	      .third-div{
	          background: red;
	          zoom:.5
	      }
	  </style>
	  <div class="first-div"></div>
	  <div class="second-div"></div>
	  <div class="third-div"></div>

具体请自己实践

#### em 和 rem 是什么
现在这个单位用的非常多，尤其是在responsible layout中。
> 1em等于当前的字体尺寸，数值的改变意味着字体大小的调整。em 有继承这个特性，也就是说，外部父元素定义了字体的em大小，内部子元素会继承这一属性的样式。
> rem = root em 。顾名思义，root即根部的，顶部的。也就是根部的em，这个根部指的是HTML根元素。所以rem的大小是针对HTML根元素的大小做字体的相对大小的调整。

	<style>
	      body{
	        font-size: 12px;  
	      }
	      /*html{
	          font-size: 12px;
	      }*/
	      div{
	          width: 200px;
	          height: 100px;
	          float:left
	      }
	      .first-div{
	          font-size: 1em
	      }
	      .second-div{
	          font-size: 2em
	      }
	      .third-div{
	          font-size: 1rem
	      }
	      .fourth-div{
	          font-size: 2rem
	      }
	  </style>
	  <div class="first-div">Hello World</div>
	  <div class="second-div">Hello World</div>
	  <div class="third-div">Hello World</div>
	  <div class="fourth-div">Hello World</div>

具体效果请自己实践。


### 参考
1. http://www.cnblogs.com/ys-ys/p/5092760.html
2. http://blog.jobbole.com/49173/

最后非常感谢两位作者，在二位基础上进行实践总结.
