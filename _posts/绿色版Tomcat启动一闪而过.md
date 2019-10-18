---
title: 绿色版Tomcat启动一闪而过
tags:
  - tomcat
  - 绿色版
category: 问题解决
abbrlink: f033e603
date: 2017-01-03 09:50:13
---

使用绿色版的Tomcat 8, 当启动startup.bat 时候一闪而过（前提是已配置好JDK的环境变量，具体配置请自行Google），我们先编辑startup.bat 文件，在文件最末尾加上**PAUSE** （截取部分代码）：

	call "%EXECUTABLE%" start %CMD_LINE_ARGS%
	:end
	PAUSE

重新启动，这样不会一闪而过，看看log上的信息，我们进行调试, 我们得到如下信息：

	Neither the JAVA_HOME nor the JRE_HOME environment variable is defined

 看看startup.bat 文件内容，该文件调用了catalina.bat文件

	set "EXECUTABLE=%CATALINA_HOME%\bin\catalina.bat"

<!-- more -->

打开catalina.bat 文件查看内容，发现该文件又调用了setclasspath.bat 文件

	call "%CATALINA_HOME%\bin\setclasspath.bat" %1

继续查看setclasspath.bat 文件：

	rem -----------------------------------------
	rem Set JAVA_HOME or JRE_HOME if not already set, ensure any provided settings
	rem are valid and consistent with the selected start-up options and set up the
	rem endorsed directory.
	rem -----------------------------------------

看来新版本安装完不会自动登记环境变量JAVA_HOME，JRE_HOME, 我们需要手动设置，添加如下代码手动设置一下环境变量：

	set JAVA_HOME=C:\Program Files\Java\jdk1.8.0_102
	set JRE_HOME=C:\Program Files\Java\jre1.8.0_102
