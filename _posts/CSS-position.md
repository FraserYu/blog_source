---
title: CSS position
tags: 'CSS,position'
category: 前端
abbrlink: 2ea89c3e
date: 2016-06-26 11:28:07
---

### 序言
作为一名Java Web 工程师，深刻的认识到我的不合格，对前端有所了解，但是每次碰到前端的问题，还是让我头疼，想往full stack方向发展，决定从这一刻起，不放过每个学习钻研的机会. never too old to learn something new.

### CSS属性之position定义
在前端开发中，CSS的position属性会被经常用到，除了前端专职人员，有多少是真正的了解，或者操作熟练呢？首先还是附上W3C的关于position的值的描述

<!-- more -->

| 值  |  描述 |
| :-- | :--------|
| absolute  | 生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。|
| fixed  | 生成绝对定位的元素，相对于**浏览器窗口**进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。|
| relative  | 生成相对定位的元素，相对于其**自身**正常位置进行定位。因此，"left:20" 会向元素的 LEFT 位置添加 20 像素。|
| static  | 默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。|
|inherit |规定应该从父元素继承 position 属性的值。 |

### DOM树及代码展示
在进行详解之前先附上一段DOM树及代码，代码可以先不看，理解DOM树的结构后对比代码看：

![DOM](http://7xkyc7.com1.z0.glb.clouddn.com/dom_dom.png)

下面是DOM树展示的代码：

		<!DOCTYPE html>
		<head><meta charset="UTF-8"></head>
		<body>
			<style>	      		  
				#example {
					float: right;
				}

				#example p {
					margin: 0 0.25em;
					padding: 0.25em 0;
				}
				#div-before,
				#div-after {
					background-color: #88d;
					color: #000;
				}

				#div-1 {
					width: 400px;
					background-color: #000;
					color: #fff;
				}

				#div-1-padding {
					padding: 10px;
				}

				#div-1a {
					background-color: #d33;
					color: #fff;
				}

				#div-1b {
					background-color: #3d3;
					color: #fff;
				}

				#div-1c {
					background-color: #33d;
					color: #fff;
				}
			</style>

			<div id="example">
				<div id="div-before">
					<p>id = div-before</p>
				</div>
				<div id="div-1">
					<div id="div-1-padding">
						<p>id = div-1</p>
						<div id="div-1a">
							<p>id = div-1a</p>
							<p>莫听穿林打叶声，何妨吟啸且徐行。竹杖芒鞋轻胜马。谁怕！一蓑烟雨任平生。 料峭春风吹酒醒，微冷，山头斜照却相迎。回首向来萧瑟处。归去，也无风雨也无晴。</p>
						</div>
						<div id="div-1b">
							<p>id = div-1b</p>
							<p>噫吁嚱，危乎高哉！蜀道之难，难于上青天！蚕丛及鱼凫，开国何茫然！尔来四万八千岁，不与秦塞通人烟。西当太白有鸟道，可以横绝峨眉巅。地崩山摧壮士死，然后天梯石栈相钩连。上有六龙回日之高标，下有冲波逆折之回川。黄鹤之飞尚不得过，猿猱欲度愁攀援。青泥何盘盘，百步九折萦岩峦。扪参历井仰胁息，以手抚膺坐长叹。问君西游何时还？畏途巉岩不可攀。但见悲鸟号古木，雄飞雌从绕林间。又闻子规啼夜月，愁空山。蜀道之难，难于上青天，使人听此凋朱颜！连峰去天不盈尺，枯松倒挂倚绝壁。飞湍瀑流争喧豗，砯崖转石万壑雷。其险也如此，嗟尔远道之人胡为乎来哉！剑阁峥嵘而崔嵬，一夫当关，万夫莫开。所守或匪亲，化为狼与豺。朝避猛虎，夕避长蛇；磨牙吮血，杀人如麻。锦城虽云乐，不如早还家。蜀道之难，难于上青天，侧身西望长咨嗟！</p>
						</div>
						<div id="div-1c">
							<p>id = div-1c</p>
						</div>
					</div>
				</div>
				<div id="div-after">
					<p>id = div-after</p>
				</div>
			</div>
		</body>
	</html>

代码初步运行起来的样式：
![初始效果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_initial1.png)

### 属性值详解
#### static
static是position的默认值，如果默认值就是没有作用吗？ 比如A、B两个页面都有ID为#div-1的div, 有一个全局的设置

	#div-1 {position: absolute;}
可是B页面要求特殊，不能用absolute，此时我们可以用static来指定其恢复默认值

	body.B #div-1 {position: static;}

####  relative
注意这个属性值的定义，是相对其自身正常位置进行定位，使用relative需要注意以下三点：
1. 元素设置了relative时，是相对于元素本身位置进行定位；
2. 元素设置了relative后，可以通过“T-R-B-L”改变元素当前所在的位置，但元素移位后，同样留有当初的物理空间位；
3. 元素设置了relative后，如果没有进行任何的“T-R-B-L”设置，元素不会进行任何位置改变。

下面我们对代码稍作改动对比效果来看：

	#div-1 {
		position:relative;
		top:20px;
		left:-40px;
	}
运行结果如下：
![运行效果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_relative1.png)
从这我们可以看到#div1 向下移动了20px，向左移动了40px，但并没有影响#div-after的位置，#div1本身的位置还在，并没有发生改变，如果改变了，那还叫什么相对自己移动了。

#### absolute
注意定义，是**相对于 static 定位以外的第一个父元素进行定位**，添加下面代码：

	#div-1a {
		position:absolute;
		top:0;
		right:0;
		width:200px;
	}
看一下运行效果：
![运行效果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_absolute1.png)
我们注意到#div-a的父元素一层一层找都是默认的static, 所以会找到根html，跑到了浏览器的右上角的位置

#### fixed
fixed很简单，就相当于浏览器的窗口进行定位，我们通常固定的navigation bar固定在浏览器窗口的顶端可以用这个属性值

#### relative和absolute的结合测试
先看下图：
![运行效果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_r_a.png)

上图做为一个实例来说明“relative”和“absolute”的关系，首先上图中共有三个div放在body内，而且他们三个div的关系是“div-1>div-2>div-3”,而且在div-3有这么一个绝对定位：
> 1. div-1与div-2都没有设置“position:relative”，此时我们的div-3绝对定位后就漂到了上图中“div-3c”的位置上；
> 2. 现在我们在div-2元素中加设置一个“position: relative”，此时我们的div-3绝对定位后就漂到了上图中的“div-3a”的位置;
> 3. 接下来把相对定位的设置换到div-1元素上，此时div-3绝对定位后就到了div-3b的位置。

这个测试也就是说：**如果一个元素绝对定位后，其参照物是以离自身最近元素是否设置了相对定位，如果有设置将以离自己最近元素定位，如果没有将往其祖先元素寻找相对定位元素，一直找到html为止**

添加如下代码再次验证这个理论：

	#div-1 {
		position:relative;
	}
	#div-1a {
		position:absolute;
		top:0;
		right:0;
		width:150px;
	}
运行效果如下：
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/dom_r_a1.png)
可以看到#div-1a跑到了#div-1的右上角

#### float
通常relative和absolute良好的满足多列div的布局，这时我们就要充分利用float这个属性：
添加如下代码：

	#div-1a {
		float:left;
		width:200px;
	}
运行结果如下：
![运行结果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_float1.png)
这种场景通常用于文字包含图片的情形，#div-1b并没有设置float属性，我们想象水流#div-1b向左上方向流淌，当碰到#div-1a障碍，#div-1b就发生变形，把#div-1a的地方留出来，其他地方继续向左上方向流动.

如果在#div-1b上也加入float属性呢？

	#div-1a {
	float:left;
		width:150px;
	}
	#div-1b {
		float:left;
		width:150px;
	}
运行结果如下：
![运行结果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_float2.png)
加上float就是一个坚固无比的固体，同样有一股力量把#div-1b向左上方推动，因为#div-1b是固体，碰到#div-a就停在那个地方了. 同时我们注意到，#div-1c和#div-after的位置也发生了变化。

可是#div-c的变动不是我们想要的，接下来：

	#div-1a {
		float:left;
		width:190px;
	}
	#div-1b {
		float:left;
		width:190px;
	}
	#div-1c {
		clear:both;
	}
运行效果如下：
![运行效果](http://7xkyc7.com1.z0.glb.clouddn.com/dom_float3.png)
在这里通过clear:both 来清除#div-c的流动。

在这里同样附上clear的属性值，改动clear的值来看页面变化效果来理解clear的真谛

| 值  |  描述 |
| :-- | :--------|
| left| 在左侧不允许浮动元素。|
| right| 在右侧不允许浮动元素。|
| both| 在左右两侧均不允许浮动元素。|
| none| 默认值。允许浮动元素出现在两侧。|
| inherit| 规定应该从父元素继承 clear 属性的值。|

参考：
1. http://www.barelyfitz.com/screencast/html-training/css/positioning/
2. http://blog.jobbole.com/49320/

感谢2位！！！
