---
title: Ionic环境搭建
tags: Ionic
category: 前端
abbrlink: '31033123'
date: 2016-03-11 15:52:50
---
## Ionic 环境的搭建
-----------------------------------------------
### 前言
本人没有做过移动端开发，更没做过Hybrid开发，带着一腔热血与热爱来研究Hybrid， 在网上查询了好多Hybrid的资料，这里还是将Ionic的基本概括放在这里*ionic是一个用来开发混合手机应用的，开源的，免费的代码库。可以优化html、css和js的性能，构建高效的应用程序，而且还可以用于构建Sass和AngularJS的优化，ionic 是一个专注于用WEB开发技术，基于HTML5创建类似于手机平台原生应用的一个开发框架。绑定了AngularJS和Sass。这个框架的目的是从web的角度开发手机应用，基于PhoneGap的编译平台，可以实现编译成各个平台的应用程序。* 这对我们web开发人员来说真的是棒极了 通过对比决定选择Ionic，一切还在摸索阶段，有好多地方还不懂为什么要这样那样配置，只知道照葫芦画瓢，不过希望最终透彻的理解画瓢的整个过程，总之还是要整个搭建过程记录下来，方便后续的回忆及反思及思考。

<!-- more -->

### 环境及软件版本概述
本人还是在Windows环境下进行的搭建，都说Node.js 在Windows环境下支持不好，整个搭建过程确实很不好，磕磕绊绊算是最终完成，还是阐述一下本人的开发环境

| 软件/系统名称      |     版本号 |  
| :-------- |:--------|
| Windows    |   Win 10 64位操作系统 |
| JDK    |  1.7 |
| Node.js    |  v4.4.0 |
| Python    |  2.7.11 |  
| AndroidSDK   |  24.4.1 |
| Apache Ant   |  1.9.1 |
| Visio Studio   | 2015 |

### 前期准备工作

1. JDK的安装及配置
> 这里不想多说，百度一下随便找一个都可以.

2. Node.js的安装
> 我下载的版本是4.4， 至于为什么没有下载最新的5， 因为在[Ionic官网](http://ionicframework.com/)上说目前还不支持5， 原文是这样说的“**install Node.js 4 (Node 5 does not work at the moment!)**”,登录[Node官网](https://nodejs.org/en/) 下载，下载成功之后就需要指定安装目录疯狂下一步就可以了, 安装Node的时候会自动将环境变量写到Path中，直接cmd命令行中输入node -v 来查看node是否安装成功

3. Python的安装
> 和Node一样，选择安装目录，安装过程中环境变量默认也是会被写到Path中的，如果没有写进去，只需要在Path中加入Pathon的安装根目录即可，如 **C:\Python27\**

4. AndroidSDK的安装及配置
> 都说AndroidSDK不好下载，我没有遇到什么下载障碍，依旧选定目录进行安装
> 这里我们需要配置一下环境变量， Path环境变量中添加两个值。分别是 Android SDK中**tools**目录的路径和**platform-tools**的路径，这里有朋友发现，在安装目录下并没有platform-tools这个目录，没关系，找到tools目录下的android.bat文件并执行，勾选上platform-tools下载即可.

5. Apache Ant的安装及配置
> 去Apache官网下载，然后解压到指定目录下，讲bin目录添加到Path中，如**E:\hybrid\apache-ant-1.9.1\bin**

6. Visio Studio 的安装
> Visio Studio的安装真是让我郁闷死， 基本上遇到了网上说的各种错误，卸掉重新安装，再卸掉再来，反复折腾了三次，最终参考了http://www.serverpals.com/blog/building-using-node-gyp-with-visual-studio-express-2015-on-windows-10-pro-x64  的文章， 毫不夸张的说，我安装了一天一宿，也许是网络不给力吧。[官网](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)选择**Download Community Free**, 下载完成后，选择自定义安装，勾选visio C++和python tools两项进行安装， good luck，希望你你们安装一切顺利。安装成功后会看到下图（真是不容易）：
> ![Visio Studio Success](http://7xkyc7.com1.z0.glb.clouddn.com/blog_ionic_QQ%E6%88%AA%E5%9B%BE20160311144905.png)

7. npm安装
> 在安装node的时候就已经安装了npm， 通过命令**npm -v** 来查看当前npm的版本， 如果不是最新的可以使用**npm update npm -g**

好了以上就是前期的准备工作，我们就正式进入Ionic

### Ionic
1. 安装Cordova
> **npm install -g cordova**

2. 安装Ionic
> **npm install -g ionic**
> 安装成功后执行ionic会看到下图
> ![Ionic](http://7xkyc7.com1.z0.glb.clouddn.com/blog_ionic_QQ%E6%88%AA%E5%9B%BE20160311150707.png)

3. 创建Ionic应用
> **ionic start myApp tabs**
> myApp: 该应用的名称， 会在你执行命令的当前目录下创建myAPP的文件夹

4. 创建Android应用
> + cd myApp
> + ionic platform add android
> + ionic build android
> + ionic emulate android
> 如果一切正常会弹出Android模拟器画面， **注意：** 这个地方myApp的名字要一样，这类似从git上download下来一个到本地

5. 创建IOS应用
> + cd myApp
> + ionic platform add ios
> + ionic build ios
> + ionic emulate ios
> 如果出现**ios-sim was not found**，可以执行**npm install -g ios-sim** 命令，同样会弹出IOS模拟器画面

### 附加说明
1. HAXM
> HAXM(Hardware Accelerated Execution Manager)的缩写, intel的硬件加速执行管理器，是一款可以使用英特尔虚拟化技术（VT）加快Android开发速度的硬件辅助虚拟化引擎（管理程序）。是AMD的CPU不能安装，在CPU中增加了控制硬件，对应开启VT的时候，启动一些模拟指令（或者新增部分基础指令）来加速运算，减少各个周期以达到优化效果。开启HAXM的时候，android模拟器的速度会明显增快, 请确保您的电脑室intel的处理器，AMD的则不可以， 如何安装呢， 请参考： [HAXM](http://www.yanyulin.info/pages/2015/03/182589620565574.html) 或 [HAXM2](http://blog.sina.com.cn/s/blog_53fa47430101hwe1.html) 或 [官网](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager)

2. 执行了ionic emulate android 在模拟器中并没有出现我的APP
> 讲ionic build android命令build好的那个路径拷贝出来，然后新开一个命令行窗口执行 ： **adb install YourPath**  这样就可以看到了


### 参考资料
+ http://www.itwap.net/ArticleContent.aspx?id=26
+ https://github.com/nodejs/node-gyp
+ http://www.runoob.com/ionic/ionic-install.html
+ http://ionicframework.com/
感谢以上作者朋友们！！！
