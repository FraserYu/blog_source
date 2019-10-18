---
title: SpringMVC搭建HelloWorld
tags: SpringMVC
category: Spring
abbrlink: '55355e47'
date: 2016-01-21 18:25:53
---
# 应用SpringMVC创建HelloWorld
-----------------------------------------------
### 前言
> 所有项目都采用Maven来管理，怎样使用Maven创建Web Project在[Maven Project](http://fraseryu.github.io/2016/01/21/Maven-Project/#more)中描述的非常清晰，这里不再累述，本文应用SpringMVC创建一个HelloWorld工程并运行（请自行创建Web Project）, 软件版本如下：
>  1. **OS: ** Windows 10
>  2. **Eclipse: ** Luna
>  3. **Spring：** 4.2.4

### Spring核心Jar包
> **Spring 的核心Jar包主要如下：**
> 1. spring-aop-4.2.4.RELEASE.jar
> 2. spring-beans-4.2.4.RELEASE.jar
> 3. spring-context-4.2.4.RELEASE.jar
> 4. spring-core-4.2.4.RELEASE.jar
> 5. spring-web-4.2.4.RELEASE.jar
> 6. spring-webmvc-4.2.4.RELEASE.jar

<!-- more -->

### 搭建HelloWorld
 1. **在pom.xml中引入Spring依赖**

任何Jar包的依赖都可以在[Maven Repository](http://www.mvnrepository.com/)中来查询，如：

![SpringDependency](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_springDependency.png),

将红色框中内容粘贴到pom.xml 中, 引入相关依赖之后的文件如下：

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>4.2.4.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>4.2.4.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>4.2.4.RELEASE</version>
		</dependency>

		<dependency>
			<groupId>org.apache.tomcat</groupId>
			<artifactId>tomcat-servlet-api</artifactId>
			<version>7.0.67</version>
		</dependency>

</br>
通过视图来理解一下上面代码会做什么，找到spring4.2.4的包

![dependency1](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_dependency1.png)

解决相关依赖，如commons-logging

 ![dependency2](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_dependency2.png)

2. **在web.xml 中编写DispatcherServlet**

这里面可以在Eclipse中安装SpringIDE，这样会有spring代码的相关提示，此处只需要用Eclipse快捷键**Alt+/** 即可提示出
 DispatcherServlet代码块，方便编写程序

		<servlet>
			<servlet-name>springDispatcherServlet</servlet-name>
			<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
			<init-param>
				<param-name>contextConfigLocation</param-name>
				<param-value>classpath:springmvc.xml</param-value>
			</init-param>
			<load-on-startup>1</load-on-startup>
		</servlet>

		<!-- Map all requests to the DispatcherServlet for handling -->
		<servlet-mapping>
			<servlet-name>springDispatcherServlet</servlet-name>
			<url-pattern>/</url-pattern>
		</servlet-mapping>



3. **编写Controller**

新建DemoController.java 文件，在类上加**@controller** 注解，同时在类和方法上加requestMapping。
package demo.controller;

		import org.springframework.stereotype.Controller;
		import org.springframework.web.bind.annotation.RequestMapping;

		@Controller
		@RequestMapping(value="/demo")
		public class DemoController {

			@RequestMapping(value="/sayHello")
			public String sayHello(){
				return "success";
			}
		}

4. **处理逻辑视图**

在Controller 的sayHello方法中return一个逻辑视图**success**， 我们需要在DispatcherServlet中配置的springmvc.xml中来对逻辑视图进行处理：

		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			xmlns:aop="http://www.springframework.org/schema/aop"
			xmlns:context="http://www.springframework.org/schema/context"
			xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
				http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd
				http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd">


			<!--配置自定义扫描包 -->
			<context:component-scan base-package="demo.controller"></context:component-scan>

			<!-- 解析逻辑视图 -->
			<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
				<property name="prefix" value="/WEB-INF/views"></property>
				<property name="suffix" value=".jsp"></property>
			</bean>

		</beans>


4. **新建success.jsp**

很简单的JSP页面

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1"%>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Insert title here</title>
	</head>
	<body>

		Hello world !!
	</body>
	</html>
5. **编辑index.jsp文件**

index.jsp文件是程序部署之后默认的欢迎页面

	<html>
	<body>
	<h2>Hello World!</h2>
		<a href="/demo/sayHello">Say hello</a>
	</body>
	</html>

6. **Project文件目录结构**

最后附上文件目录结构

![project catalog](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_catalog.png)

7. **部署project并访问**

发布到Tomcat上，并访问http://localhost:8080/springMVC1/

![index.jsp](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_indexjsp.png)
and

![success.jsp](http://7xkyc7.com1.z0.glb.clouddn.com/blog_4_SpringMVC_successjsp.png)

到此一个简单的Hello World的工程搭建完毕，有问题请留言沟通，逐步进阶SpringMVC。
