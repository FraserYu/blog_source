---
title: Maven方式整合SpringMVC+Spring+Mybatis
tags:
  - SpringMVC
  - maven
  - Spring
  - Mybatis
  - SpringMVC国际化
  - 数据验证
  - Log4j
category: 构建工具
abbrlink: a339db2d
date: 2016-08-27 18:02:24
---

### 背景
一直在从事Java Web 开发工作，却一直使用项目中架构师搭建的基本框架或者一些产品，使用的过程中不会有任何问题，所有的代码写到后期都是针对特定业务逻辑的复制与粘贴，可是当自己想搭建框架，看到满满的配置文件，看着熟悉，却不理解他的一些基本原理，只是照葫芦画瓢，很难满足内心对于框架的那种原始渴望，在项目中用过Struts1，Struts2，和当下很流行的SpringMVC框架，浏览大量博客，及一些视频教程整合出相对更符合实际项目需要的项目，所以有必要仔细研究一下，并且记录整合的过程及一些细节问题。

### 目的
当前的目标成为Full Stack engineer，同时成为Java Web 后端的架构师，积小流成江海，特此总结与更新，与广大的朋友交流学习。在实现目标的道路上不断进步。

<!-- more -->

### 整合内容
| 内容|     状态 |   
| :-------- |: --------|
| SpringMVC数据校验|   Done|
| Log4j|   Done|
| SpringMVC国际化|   Done|
| Spring与Mybatis整合 |   Done|
| mybatis-generator |   Done|
| Spring事务 |  TODO|
| SpringSecurity|   TODO|
| CronJob(定时任务) |   TODO|
| others| TODO |

### 基本环境及开发工具
| 工具|     版本号 |   
| :-------- |: --------|
| Windows|   10 X64位|
| JDK|   1.7|
| IntelliJ IDEA|   2016|
| Tomcat |   8|
| Mysql |   5.7.14|
| Maven |   3.3.9|
| SpringMVC|   4.3.2|
| Spring |   4.3.2|
| Mybatis|   3.4.1|


### 简单认识框架
#### SpringMVC
SpringMVC与Struts的对比大家真的可以自行Google就可以了，实现的基本原理可以参考我之前的文章**[SpringMVC](http://fraserlife.com/2016/01/15/SpringMVC1/)**，里面的一些映射关系需要了解

#### Spring
Spring是非常强大的，可以整合非常多的功能，依赖注入及AOP大家谈烂的功能好多却都没有嚼烂消化掉，推荐进入**[Spring官网](https://spring.io/docs)**看看描述

#### Mybatis
MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录，**简单总结的说就是节省了我们大量建立JDBC driver,建立连接等基本工作**，在此不得不要多说一下mybatis，因为后面的好多配置和以下内容相关，了解这个地方对配置文件将会有更深的理解：
> 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为中心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例。在这个configuration的XML文件里包含了含获取数据库连接实例的数据源（DataSource）和决定事务范围和控制方式的事务管理器（TransactionManager）的核心配置

首先来看一下Mybatis configuration XML文件的内容，**先混个关键字眼熟，千万先别在此追究细节**：

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE configuration
	  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	  "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	  <environments default="development">
	    <environment id="development">
	      <transactionManager type="JDBC"/>
	      <dataSource type="POOLED">
	        <property name="driver" value="${driver}"/>
	        <property name="url" value="${url}"/>
	        <property name="username" value="${username}"/>
	        <property name="password" value="${password}"/>
	      </dataSource>
	    </environment>
	  </environments>
	  <mappers>
	    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
	  </mappers>
	</configuration>

这里面有**transactionManager 、dataSource 、mappers**这类关键字

这里我们首先要通过mybatis-generator来自动生成mapper及mapping映射文件，请参考我之前的文章**[Mybatis-generator](http://fraserlife.com/2016/08/29/Mybatis%E7%94%9F%E6%88%90mapper/#more)**

### 文件配置
首先来看一下项目的整体目录结构及说明
![目录结构](http://7xkyc7.com1.z0.glb.clouddn.com/frame_frame2.png)


#### 引入依赖包
我先把一些基本的核心包罗列在此，后面会有pom.xml文件的全部内容，大家不用担心
1. Spring核心包

	      <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-core</artifactId>
	          <version>4.3.2.RELEASE</version>
	      </dependency>

2. Mybatis与Spring关联包

	      <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
	      <dependency>
	          <groupId>org.mybatis</groupId>
	          <artifactId>mybatis-spring</artifactId>
	          <version>1.3.0</version>
	      </dependency>

    这里有一个说明，因为mybatis-spring1.3.0中没有Compile Dependencies，所以它的依赖包还需要手动引入一下，mybatis-spring1.1.1就有，所以参考1.1.1再引入如下jar包

	      <!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-tx</artifactId>
	          <version>4.3.2.RELEASE</version>
	      </dependency>
	      <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
	      <dependency>
	          <groupId>org.springframework</groupId>
	          <artifactId>spring-jdbc</artifactId>
	          <version>4.3.2.RELEASE</version>
	      </dependency>  
因为我们会用到Druid数据源和Mysql，所以我们还需要他们的包

	      <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
	      <dependency>
	          <groupId>mysql</groupId>
	          <artifactId>mysql-connector-java</artifactId>
	          <version>5.1.38</version>
	      </dependency>

	      <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
	      <dependency>
	          <groupId>com.alibaba</groupId>
	          <artifactId>druid</artifactId>
	          <version>1.0.25</version>
	      </dependency>	      
3. Mybatis包

	      <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
	      <dependency>
	          <groupId>org.mybatis</groupId>
	          <artifactId>mybatis</artifactId>
	          <version>3.4.1</version>
	      </dependency>

#### Spring配置
spring.xml文件内容如下：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd
	       http://www.springframework.org/schema/context
	       http://www.springframework.org/schema/context/spring-context.xsd">

	    <!-- import properties file -->
	    <context:property-placeholder location="classpath:properties/config.properties" />

	    <!-- Scan service layer -->
	    <context:component-scan base-package="com.demo.service" />

	</beans>

这里主要通过上下文引入项目配置文件config.properties及扫描service目录及其子目录（这样在service层的类文件上加如**@Service**标签，spring就会把它注入成一个Java bean），其中config.properties主要内容如下：

	validationQuery=SELECT 1
	jdbc_driverClassName=com.mysql.jdbc.Driver
	jdbc_url=jdbc:mysql://localhost:3306/frame?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&autoReconnect=true&createDatabaseIfNotExist=true
	jdbc_username=root
	jdbc_password=000000

#### Mybatis与Spring的整合配置
这个文件里面会有大量的内容，配置文件会有注释同时下面也会有重点说明：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:tx="http://www.springframework.org/schema/tx"
	       xmlns:aop="http://www.springframework.org/schema/aop"
	       xsi:schemaLocation="
	       http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
	       http://www.springframework.org/schema/tx
	       http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	       http://www.springframework.org/schema/aop
	       http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">


	    <!-- JNDI方式配置数据源 -->
	    <!-- <bean id="dataSource" class="org.springframework.jndi.JndiObjectFactoryBean">
	        <property name="jndiName" value="${jndiName}"></property> </bean> -->

	    <!-- 配置数据源 -->
	    <bean name="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
	          init-method="init" destroy-method="close">
	        <property name="driverClassLoader" value="${jdbc_driverClassName}"/>
	        <property name="url" value="${jdbc_url}" />
	        <property name="username" value="${jdbc_username}" />
	        <property name="password" value="${jdbc_password}" />

	        <!-- 初始化连接大小 -->
	        <property name="initialSize" value="0" />
	        <!-- 连接池最大使用连接数量 -->
	        <property name="maxActive" value="20" />
	        <!-- 连接池最大空闲 -->
	        <property name="maxIdle" value="20" />
	        <!-- 连接池最小空闲 -->
	        <property name="minIdle" value="0" />
	        <!-- 获取连接最大等待时间 -->
	        <property name="maxWait" value="60000" />

	        <!-- <property name="poolPreparedStatements" value="true" /> <property
	            name="maxPoolPreparedStatementPerConnectionSize" value="33" /> -->

	        <property name="validationQuery" value="${validationQuery}" />
	        <property name="testOnBorrow" value="false" />
	        <property name="testOnReturn" value="false" />
	        <property name="testWhileIdle" value="true" />

	        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
	        <property name="timeBetweenEvictionRunsMillis" value="60000" />
	        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
	        <property name="minEvictableIdleTimeMillis" value="25200000" />

	        <!-- 打开removeAbandoned功能 -->
	        <property name="removeAbandoned" value="true" />
	        <!-- 1800秒，也就是30分钟 -->
	        <property name="removeAbandonedTimeout" value="1800" />
	        <!-- 关闭abanded连接时输出错误日志 -->
	        <property name="logAbandoned" value="true" />

	        <!-- 监控数据库 -->
	        <!-- <property name="filters" value="stat" /> -->
	        <property name="filters" value="mergeStat" />
	    </bean>

	    <!-- myBatis文件 -->
	    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	        <property name="dataSource" ref="dataSource" />
	        <!-- 自动扫描entity目录, 省掉Configuration.xml里的手工配置 -->
	        <property name="mapperLocations" value="classpath:com/demo/dao/mapingXML/*.xml" />
	    </bean>

	    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	        <property name="basePackage" value="com.demo.dao" />
	        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
	    </bean>

	    <!-- 配置事务管理器 -->
	    <bean id="transactionManager"
	          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	        <property name="dataSource" ref="dataSource" />
	    </bean>

	    <!-- 注解方式配置事物 -->
	    <!-- <tx:annotation-driven transaction-manager="transactionManager" /> -->

	    <!-- 拦截器方式配置事物 -->
	    <tx:advice id="transactionAdvice" transaction-manager="transactionManager">
	        <tx:attributes>
	            <tx:method name="add*" propagation="REQUIRED" />
	            <tx:method name="append*" propagation="REQUIRED" />
	            <tx:method name="insert*" propagation="REQUIRED" />
	            <tx:method name="save*" propagation="REQUIRED" />
	            <tx:method name="update*" propagation="REQUIRED" />
	            <tx:method name="modify*" propagation="REQUIRED" />
	            <tx:method name="edit*" propagation="REQUIRED" />
	            <tx:method name="delete*" propagation="REQUIRED" />
	            <tx:method name="remove*" propagation="REQUIRED" />
	            <tx:method name="repair" propagation="REQUIRED" />
	            <tx:method name="delAndRepair" propagation="REQUIRED" />

	            <tx:method name="get*" propagation="SUPPORTS" />
	            <tx:method name="find*" propagation="SUPPORTS" />
	            <tx:method name="load*" propagation="SUPPORTS" />
	            <tx:method name="search*" propagation="SUPPORTS" />
	            <tx:method name="datagrid*" propagation="SUPPORTS" />

	            <tx:method name="*" propagation="SUPPORTS" />
	        </tx:attributes>
	    </tx:advice>
	    <aop:config>
	        <aop:pointcut id="transactionPointcut" expression="execution(* com.demo.service..*Impl.*(..))" />
	        <aop:advisor pointcut-ref="transactionPointcut" advice-ref="transactionAdvice" />
	    </aop:config>


	    <!-- 配置druid监控spring jdbc -->
	    <bean id="druid-stat-interceptor"
	          class="com.alibaba.druid.support.spring.stat.DruidStatInterceptor">
	    </bean>
	    <bean id="druid-stat-pointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut"
	          scope="prototype">
	        <property name="patterns">
	            <list>
	                <value>com.demo.service.*</value>
	            </list>
	        </property>
	    </bean>
	    <aop:config>
	        <aop:advisor advice-ref="druid-stat-interceptor" pointcut-ref="druid-stat-pointcut" />
	    </aop:config>
	</beans>

*下面1/2/3点内容正是前面mybatis的configuration要做的事情，只不过和spring整合稍稍有些变化*
1. **datasource：**这里我们使用了Alibaba的Druid作为数据源，当然也有c3p0和DBCP等作为数据源，请自行比较这几种数据源的优缺点
2. **sqlSessionFactory：** 这里我们看到使用sqlSessionFactoryBean来创建实例，而且通过mapperLocations属性自动扫描dao下所有的xml文件
3. **transactionManager：** 创建事务管理器
4. **MapperScannerConfigurer：**来扫描dao下的所有mapper 接口
5. **tx:advice：** 有了事务管理器，我们需要配置拦截哪些方法作为事务处理，通常在service层实现类上加@transactional注解来告知spring这个类里面的以add/insert/update等名称开头的方法要做事物的处理，**aop:pointcut**标签正是扫描service层的实现类
6.   最后一部分是Druid的jdbc监听，会在其他地方解释

至此Spring 与 Mybatis的整合结束了.

#### 整合SpringMVC
SpringMVC的整合就相对简单的多了，先看一下web.xml 文件：

	<!-- Load Spring context file-->
	    <context-param>
	        <param-name>contextConfigLocation</param-name>
	        <param-value>classpath:config/web-application-config.xml</param-value>
	        <description>
	            The 'contextConfigLocation' param specifies where your configuration files are located.
	        </description>
	    </context-param>

	<!--ContextLoaderListener implements javax.servlet.ServletContextListener interface, load global application context -->
	    <listener>
	        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	    </listener>	 

	     <servlet>
	        <servlet-name>frame</servlet-name>
	        <description>
	            DispatcherServlet,Spring MVC dispatcher servlet. This is the entry point for the Spring MVC application.
	        </description>
	        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	        <init-param>
	             <param-name>contextConfigLocation</param-name>
	            <param-value></param-value>
	        </init-param>
	        <load-on-startup>1</load-on-startup>
	    </servlet>

	    <servlet-mapping>
	        <servlet-name>frame</servlet-name>
	        <url-pattern>/</url-pattern>
	    </servlet-mapping>

1. 配置**DispatcherServlet**
2. 配置Spring的**ContextLoaderListener** 上下文监听
3. 配置**context-param** 来指定****ContextLoaderListener****要加载的SpringMVC文件的位置，这里我们定义了一个文件叫**web-application-config.xml**

我们来看看**web-application-config.xml**的内容

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:mvc="http://www.springframework.org/schema/mvc"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	            http://www.springframework.org/schema/beans/spring-beans.xsd">

	    <!--Import additional configuration files-->
	    <import resource="springmvc-config.xml"></import>
	    <import resource="spring.xml"></import>
	    <import resource="spring-mybatis.xml"></import>

	</beans>

这里我们加载了三个文件，其中**spring.xml**和**spring-mybatis.xml**上面讲过了，这里着重说一下**springmvc-config.xml**，来看一下内容：

	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:mvc="http://www.springframework.org/schema/mvc"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans
	       http://www.springframework.org/schema/beans/spring-beans.xsd
	       http://www.springframework.org/schema/mvc
	       http://www.springframework.org/schema/mvc/spring-mvc.xsd
	       http://www.springframework.org/schema/context
	       http://www.springframework.org/schema/context/spring-context.xsd">

	    <!-- Section for Traditional Spring MVC. -->
	    <!-- Annotation driven controllers -->
	    <mvc:annotation-driven validator="validator">
	        <mvc:message-converters>
	            <bean class="org.springframework.http.converter.ResourceHttpMessageConverter"/>
	            <bean class="org.springframework.http.converter.xml.Jaxb2RootElementHttpMessageConverter"/>
	            <!-- Avoid downloading file when IE execute ajax request and response json data-->
	            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
	                <property name="supportedMediaTypes">
	                    <list>
	                        <value>text/html;charset=UTF-8</value>
	                        <value>application/json;charset=UTF-8</value>
	                    </list>
	                </property>
	            </bean>
	        </mvc:message-converters>
	    </mvc:annotation-driven>

	    <!-- a list of packages to scan. Can be extended by adding (or removing) packages. -->
	    <context:component-scan base-package="com.demo"></context:component-scan>

	    <mvc:resources mapping="/ui/**" location="/ui/**"></mvc:resources>

	    <!-- Tell Spring MVC how to find its jsp files -->
	    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"></property>
	        <property name="prefix" value="/WEB-INF/jsp/"></property>
	        <property name="suffix" value=".jsp"></property>
	    </bean>

	    <!--
	    I18n using MessageSource
	    As an alternative to ResourceBundleMessageSource, Spring provides a ReloadableResourceBundleMessageSource class.
	    This variant supports the same bundle file format but is more flexible than the standard JDK based ResourceBundleMessageSource implementation.
	    In particular, it allows for reading files from any Spring resource location (not just from the classpath) and supports hot reloading of bundle property files (while efficiently caching them in between)
	    -->

	    <bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
	        <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
	        <!-- If no validationMessageSource was referred, it will search ValidationMessages.properties in classpath-->
	        <property name="validationMessageSource" ref="messageSource" />
	    </bean>

	    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
	        <property name="basenames" >
	            <list>
	                <value>I18n.business</value>
	                <value>I18n.exception</value>
	                <value>I18n.validationMessages</value>
	            </list>
	        </property>
	        <property name="defaultEncoding" value="UTF-8"></property>
	    </bean>


	    <bean id="commonsMultipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	        <property name="defaultEncoding">
	            <value>UTF-8</value>
	        </property>
	        <property name="maxUploadSize">
	            <value>32505856</value>
	        </property>
	        <property name="maxInMemorySize">
	            <value>4096</value>
	        </property>
	    </bean>

	</beans>

1. **mvc:annotation-driven** 标签告知会采用Spring注解形式
2. **context:component-scan** 扫描包，加入Spring注解就可以解释成bean
3. **mvc:resources** 扫描静态资源
4. **InternalResourceViewResolver** SpringMVC通过它将Controller指定的逻辑视图解释成物理视图，同时在物理视图中应用JSTL来渲染，这些物理视图存放在**/WEB-INF/jsp/** 目录下，同时后缀为**.jsp**的文件
5. **LocalValidatorFactoryBean** SpringMVC的国际化，指定三种文件**business(业务说明国际化)**,** exception（异常信息国际化）**,**validationMessages（数据验证国际化）**
6. **CommonsMultipartResolver** SpringMVC的文件上传，不久都会演示示例

#### 配置log4j
在web.xml 中添加如下信息：

	<!-- Load log4j.xml file -->
	    <context-param>
	        <param-name>log4jConfigLocation</param-name>
	        <param-value>classpath:config/log4j.xml</param-value>
	    </context-param>
	    <!-- Read log4j.xml file with specified interval -->
	    <context-param>
	        <param-name>log4jRefreshInterval</param-name>
	        <param-value>60000</param-value>
	    </context-param>

		</listener>
	    <!-- Listener for Log4j-->
	    <listener>
	        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
	    </listener>

1. **Log4jConfigListener** 配置Spring的Log4j监听器
2. **log4jConfigLocation**加载log4j.xml 文件
3. **log4jRefreshInterval** 间隔多长时间重读log4j.xml 文件

来看一下log4j.xml 文件的内容（读不懂的请单独查询log4j）：

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd" >
	<log4j:configuration>
	    <appender name="stdout" class="org.apache.log4j.ConsoleAppender">
	        <param name="Target" value="System.out"/>
	        <param name="Append" value="true"/>
	        <param name="DatePattern" value=".yyyy-MM-dd" />
	        <layout class="org.apache.log4j.PatternLayout">
	            <param name="ConversionPattern" value="%d %-5p %c{1}:%L %m %n" />
	            <!--
	            ConversionPattern format specification
	            %d      inserts the date; you can specify the format (%d{yyyy-MM-dd HH:mm:ss,SSS})
	            %-5p    inserts the priority log level, 5 characters, left justified
	            %c{1}   inserts the name of the class
	            %L      inserts the line number
	            %m      inserts the user message
	            %n      inserts the separator (for example, a new line)
	            -->
	        </layout>
	    </appender>

	    <appender name="fileDaily" class="org.apache.log4j.DailyRollingFileAppender">
	        <param name="File" value="${webapp.root}/WEB-INF/logs/frame-date.log"/>
	        <param name="Append" value="true"/>
	        <param name="MaxFileSize" value="4096KB" />
	        <param name="MaxBackupIndex" value="10" />
	        <param name="DatePattern" value=".yyyy-MM-dd" />
	        <layout class="org.apache.log4j.PatternLayout">
	            <param name="ConversionPattern" value="%d [%t] %-5p %c - %m%n"/>
	        </layout>
	    </appender>

	    <appender name="fileAppender" class="org.apache.log4j.RollingFileAppender">
	        <param name="Threshold" value="INFO" />
	        <param name="MaxFileSize" value="4096KB" />
	        <param name="MaxBackupIndex" value="10" />
	        <param name="File" value="${webapp.root}/WEB-INF/logs/frame.log"/>
	        <layout class="org.apache.log4j.PatternLayout">
	            <param name="ConversionPattern" value="%d %-5p %c{1}:%L %m %n" />
	        </layout>
	    </appender>

	    <!--sets the priority log level for org.springframework-->
	    <logger name="org.springframework">
	        <level value="info"/>
	    </logger>

	    <!--sets the priority log level for it.helloworld.controller-->
	    <logger name= "com.demo">
	        <level value="debug"/>
	    </logger>

	    <!--sets the default priority log level-->
	    <root>
	        <priority value="debug"></priority>
	        <appender-ref ref="stdout"/>
	        <appender-ref ref="fileDaily"/>
	        <appender-ref ref="fileAppender"/>
	    </root>
	</log4j:configuration>

1. **${webapp.root}**使用了项目的相对目录，这样比指定绝对目录更好

至此所有的配置全部结束，接下来就要做演示了

### 编写代码

1. 编写index.jsp

		<%@ page contentType="text/html;charset=UTF-8"%>
		<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
		<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
		<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
		<html>
		<head>
		    <meta http-equiv="Content-Type" content="text/html charset=UTF-8">
		</head>
		<body>
		<h2>Hello Fraser</h2>

		<a href="/demo/user/add"><spring:message code="user.add"></spring:message></a>
		</body>
		</html>

2. 编写addUser.jsp

		<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
		<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
		<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
		<%--
		  Created by IntelliJ IDEA.
		  User: fraser.yu
		  Date: 8/21/2016
		  Time: 1:36 PM
		  To change this template use File | Settings | File Templates.
		--%>
		<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
		<html>
		<head>
		    <title><spring:message code="user.add"></spring:message> </title>
		</head>
		<body>
		    <c:url value="/demo/user/add" var="addUserUrl"/>
		    <form:form method="post" modelAttribute="user" action="${addUserUrl}">
		        <spring:message code="user.username" text="Username"/>
		        <form:input path="username"/>
		        <form:errors path="username"></form:errors><br>

		        <spring:message code="user.password" text="Password"/>
		        <form:input path="password"/>
		        <form:errors path="password"></form:errors><br>

		        <spring:message code="user.email" text="Email"/>
		        <form:input path="email"/>
		        <form:errors path="email"></form:errors><br>

		        <spring:message code="user.id" text="ID"/>
		        <form:input path="id"/>
		        <form:errors path="id"></form:errors><br>

		        <input type="submit" value="<spring:message code='user.submit'/>"/><br>
		    </form:form>
		</body>
		</html>

3. 编写userList.jsp

		<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
		<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
		<%--
		  Created by IntelliJ IDEA.
		  User: fraser.yu
		  Date: 8/21/2016
		  Time: 2:47 PM
		  To change this template use File | Settings | File Templates.
		--%>
		<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
		<html>
		<head>
		    <title><spring:message code="user.list" text="User List"></spring:message> </title>
		</head>
		<body>

		    <c:forEach items="${users}" var="user">
		        ${user.id}——${user.username}——${user.email}——${user.nickname}<br>
		    </c:forEach>
		</body>
		</html>

4. 编写DemoController.java

		package com.demo.controller;

		import com.demo.entity.User;
		import com.demo.service.DemoService;
		import org.apache.log4j.Logger;
		import org.springframework.stereotype.Controller;
		import org.springframework.ui.Model;
		import org.springframework.validation.BindingResult;
		import org.springframework.validation.annotation.Validated;
		import org.springframework.web.bind.annotation.RequestMapping;
		import org.springframework.web.bind.annotation.RequestMethod;
		import org.springframework.web.servlet.ModelAndView;

		import javax.annotation.Resource;
		import java.util.List;
		/**
		 * Created by fraser.yu on 8/20/2016.
		 */

		@Controller
		@RequestMapping(value = "/demo")
		public class DemoController {

		    protected static final Logger logger = Logger.getLogger(DemoController.class);
		    private User user;
		    @Resource
		    private DemoService demoService;

		    @RequestMapping(value = "user/add", method = RequestMethod.GET)
		    public ModelAndView addUser(Model model){
		        user = new User();
		        model.addAttribute("user",user);
		        logger.info("jump to add user page");
		        return new ModelAndView("demo/addUser");
		    }

		    @RequestMapping(value = "user/add", method = RequestMethod.POST)
		    public ModelAndView addUser(Model model, @Validated User user, BindingResult bindingResult){
		        if (bindingResult.hasErrors()){
		            return new ModelAndView("demo/addUser");
		        }
		        demoService.insertSelective(user);
		        logger.info("insert user to db");
		        return new ModelAndView("redirect: /demo/user/userList ");
		    }

		    @RequestMapping(value = "user/userList", method = RequestMethod.GET)
		    public ModelAndView userList(Model model){
		        List<User> users = demoService.selectAllUser();
		        model.addAttribute("users",users);
		        return new ModelAndView("demo/userList");
		    }
		}

5. 编写DemoService.java 接口

		package com.demo.service;

		import com.demo.entity.User;

		import java.util.List;

		/**
		 * Created by fraser.yu on 8/31/2016.
		 */
		public interface DemoService {

		    int insertSelective(User record);

		    List<User> selectAllUser();
		}

6. 编写DemoServiceImpl.java实现类

		package com.demo.service.impl;

		import com.demo.dao.UserMapper;
		import com.demo.entity.User;
		import com.demo.service.DemoService;
		import org.springframework.stereotype.Service;

		import javax.annotation.Resource;
		import java.util.List;

		/**
		 * Created by fraser.yu on 8/31/2016.
		 */
		@Service
		public class DemoServiceImpl implements DemoService{

		    @Resource
		    private UserMapper userMapper;

		    public int insertSelective(User record) {
		        return userMapper.insertSelective(record);
		    }

		    public List<User> selectAllUser() {
		        return userMapper.selectUsers();
		    }
		}

7. Mapper及mapping.xml 文件已经用mybatis-generator生成，在此不继续描述
8. 编写需要做数据验证的User.java POJO类（验证规则请参考JSR303）

		package com.demo.entity;

		import org.hibernate.validator.constraints.Email;
		import org.hibernate.validator.constraints.NotEmpty;

		import javax.validation.constraints.Size;
		import java.util.Date;

		public class User {
		    @NotEmpty
		    private String id;
		    @NotEmpty
		    private String username;
		    @Email
		    private String email;
		    @Size(min = 6, max = 10)
		    private String password;

		    private Date createTime;
		    private String nickname;

		    public String getId() {
		        return id;
		    }

		    public void setId(String id) {
		        this.id = id == null ? null : id.trim();
		    }

		    public String getUsername() {
		        return username;
		    }

		    public void setUsername(String username) {
		        this.username = username == null ? null : username.trim();
		    }

		    public String getEmail() {
		        return email;
		    }

		    public void setEmail(String email) {
		        this.email = email == null ? null : email.trim();
		    }

		    public String getPassword() {
		        return password;
		    }

		    public void setPassword(String password) {
		        this.password = password == null ? null : password.trim();
		    }

		    public Date getCreateTime() {
		        return createTime;
		    }

		    public void setCreateTime(Date createTime) {
		        this.createTime = createTime;
		    }

		    public String getNickname() {
		        return nickname;
		    }

		    public void setNickname(String nickname) {
		        this.nickname = nickname == null ? null : nickname.trim();
		    }
		}

9. 编写国际化验证文件validationMessages_en.properties

		NotEmpty.user.username=Username can NOT be empty
		NotEmpty.user.password=Password can NOT be empty
		NotEmpty.user.id=User ID can NOT be empty
		Email.user.email=NOT a well-formed email address
		Size.user.password=Password length should be between 6 and 10

### 演示
1. 进入首页
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_1.png)

2. 点击Add User
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_2.png)

3. 什么也不填写，直接点击Submit
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_3.png)

4. 填写内容
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_4.png)

5. 再次点击Submit
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_5.png)

至此SpringMVC的整合到此结束，后续还会继续加入Spring-Security和Spring事务管理方面的知识。

### 搭建过程中遇到的问题与解决办法
1. 问题一

		Caused by: java.lang.UnsupportedClassVersionError: com/mysql/cj/jdbc/Driver : Unsupported major.minor version 52.0 (unable to load class com.mysql.cj.jdbc.Driver)
			at org.apache.catalina.loader.WebappClassLoaderBase.findClassInternal(WebappClassLoaderBase.java:2544)
			at org.apache.catalina.loader.WebappClassLoaderBase.findClass(WebappClassLoaderBase.java:858)
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1301)
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1166)
			at java.lang.Class.forName0(Native Method)
			at java.lang.Class.forName(Class.java:191)
			at com.alibaba.druid.util.Utils.loadClass(Utils.java:222)
			at com.alibaba.druid.util.JdbcUtils.getDriverClassName(JdbcUtils.java:370)
			at com.alibaba.druid.pool.DruidDataSource.init(DruidDataSource.java:636)
			at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
			at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
			at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
			at java.lang.reflect.Method.invoke(Method.java:606)
			at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeCustomInitMethod(AbstractAutowireCapableBeanFactory.java:1706)
			at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1645)
			at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1574)
			... 55 more
**解决方案：** 更换MySQL Connector jar包的版本，我从6.0.25换成了5.1.38

2. 问题二

			Caused by: java.lang.ClassNotFoundException: com.mysql.jdbc.Driver
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1332)
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1166)
			at java.lang.Class.forName0(Native Method)
			at java.lang.Class.forName(Class.java:191)
			at com.alibaba.druid.util.JdbcUtils.createDriver(JdbcUtils.java:558)
			... 63 more

			Caused by: java.lang.ClassNotFoundException: com.alibaba.druid.support.spring.stat.DruidStatInterceptor
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1332)
			at org.apache.catalina.loader.WebappClassLoaderBase.loadClass(WebappClassLoaderBase.java:1166)
			at org.springframework.util.ClassUtils.forName(ClassUtils.java:250)
			at org.springframework.beans.factory.support.AbstractBeanDefinition.resolveBeanClass(AbstractBeanDefinition.java:394)
			at org.springframework.beans.factory.support.AbstractBeanFactory.doResolveBeanClass(AbstractBeanFactory.java:1402)
			at org.springframework.beans.factory.support.AbstractBeanFactory.resolveBeanClass(AbstractBeanFactory.java:1349)
			... 72 more
**解决方案：** 不知道IntelliJ IDEA集成maven为什么有些jar包不自动部署进去，按照下面的图将没有加进去的包放进去![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/frame_jar.png)

3. 问题三

		org.apache.ibatis.binding.BindingException: Invalid bound statement (not found):
**解决方案：**IntelliJ IDEA集成maven是自动将src/main/java下的java文件 和 src/main/resource 下的xml文件部署到发布目录，所以mybatis-generator生成的在src/main/java/demo/dao/mappingxml/下的mapping.xml文件不会自动部署进去，更改pom.xml 文件，此处pom.xml文件作如下添加：

		<resources>
		          <resource>
		              <directory>src/main/resources</directory>
		              <includes>
		                  <include>**/*.properties</include>
		                  <include>**/*.xml</include>
		                  <include>**/*.tld</include>
		              </includes>
		              <!-- Here we must specify false, otherwise it has db connection error -->
		              <filtering>false</filtering>
		          </resource>
		          <resource>
		              <directory>src/main/java</directory>
		              <includes>
		                  <include>**/*.properties</include>
		                  <include>**/*.xml</include>
		                  <include>**/*.tld</include>
		              </includes>
		              <filtering>false</filtering>
		          </resource>
		      </resources>
更多可以参考我的文章：**[Maven include 和 exclude指定文件](http://fraserlife.com/2016/08/31/maven-include-exclude%E6%96%87%E4%BB%B6/)**

4. 问题四
有些时候部署的文件不是最新的，所以删除整个target目录，然后重新部署。

### 项目下载
请在我的GitHub上下载： https://github.com/FraserYu/frame.git

同时请看一下如下文章：**[spring3 restful 服务迁移到 spring4需要注意的事项](http://ju.outofmemory.cn/entry/64313)**
