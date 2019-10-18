---
title: SpringMVC
tags: SpringMVC
category: Spring
abbrlink: ae0f95e0
date: 2016-01-15 17:12:16
---
SpringMVC (一) ：初探SpringMVC
-----------------------------------------------
### 什么是MVC
还是先说一小段基本概念，模型-视图-控制器（MVC）是一个众所周知的以设计界面应用程序为基础的设计模式。它主要通过分离模型、视图及控制器在应用程序中的角色将业务逻辑从界面中解耦。通常情况下：
> 1. 模型负责封装应用程序数据在视图层展示。
> 2. 视图仅仅只是展示这些数据，不包含任何业务逻辑。
> 3. 控制器负责接收来自用户的请求，并调用后台服务（serivce或者dao）来处理业务逻辑。处理后，后台业务层可能会返回了一些数据在视图层展示。控制器收集这些数据及准备模型在视图层展示。

<!-- more -->

MVC模式的核心思想是将业务逻辑从界面中分离出来，允许它们单独改变而不会相互影响。

### 进一步来分析MVC
首先我们知道Web应用分为三层：
> 1. 表现层 （UI）
> 2. 业务逻辑层（Business logic layer）
> 3. 数据访问层 （Data Access Layer）

在Spring MVC应用程序中，模型通常由POJO对象或者Bean组成，它在业务层中被处理，在持久层中被持久化。视图通常是用JSP标准标签库（JSTL）编写的JSP模板。控制器部分是由dispatcher servlet负责。

**注意**：一些开发人员认为业务逻辑层和数据获取层是MVC模型的一部分，我个人认为，这两层是MVC之外的扩展，具体的理解请看下图：

![Web三层](http://7xkyc7.com1.z0.glb.clouddn.com/blog_2_SpringMVC%28%E4%B8%80%29_logic.png)

### Dispatcher Servlet(Spring控制器)的工作原理
在最简单的Spring MVC应用程序中，控制器是**唯一**的你需要在Java web部署描述文件（即web.xml文件）中配置的Servlet。Spring MVC控制器通常称作Dispatcher Servlet，实现了前端控制器设计模式。并且每个web请求必须通过它以便它能够管理整个请求的**生命周期**。

![lifeCycle](http://7xkyc7.com1.z0.glb.clouddn.com/blog_2_SpringMVC%28%E4%B8%80%29_lifecycle.png)

当一个web请求发送到Spring MVC应用程序，dispatcher servlet首先接收请求。然后它组织那些在Spring web应用程序上下文配置的（例如实际请求处理控制器和视图解析器）或者使用注解配置的组件，所有的这些都需要处理该请求。
> 1. 一个Dispatcher servlet 可以对应一个或多个Handler Mapping
> 2. 一个Handler Mapping 可以对应一个或多个Handler 方法

在Spring3.0中定义一个控制器类，这个类必须标有@Controller注解，流程图详解如下：
>1. 请求过来，通过Handler Mapping，通过匹配，返回匹配到的Controller,
>2. 继续向controller发送请求，找到handler method, 方法返回一个ModelAndView 的逻辑视图名称
>3. 将视图名称View Name 转交给View Resolver 视图解析器来处理，处理完返回一个View所需数据对象
>4. 将数据Model交个View来处理进行数据渲染，然后返回给Dispatcher Servlet.
>5. 最后，将结果response给终端用户

理解了这个逻辑，也就算是理解了SpringMVC的基本工作原理，接下来就是要究细节的时候了。
