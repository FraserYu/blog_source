---
title: IntelliJ IDEA提示端口被占用
tags:
  - IntelliJ IDEA
  - port
category: 问题解决
abbrlink: b76b5087
date: 2016-08-31 14:54:29
---

与其说IntelliJ IDEA提示端口被占用，不如说在Windows环境下怎样查看被占用的端口以及Kill PID(进程号)
1. 查看被占用端口

		D:\Users\fraser.yu>netstat -aon|findstr "9050"
		  TCP  0.0.0.0:9050   0.0.0.0:0   LISTENING   1696
我们看到占用端口**9050**的是进程号为**1696**的进程.
2. 确定任务
通过以下命令就知道是哪项任务的进程号是**1696**

		D:\Users\fraser.yu>tasklist|findstr "1696"
		java.exe   1696 Console  4   346,688 K
3. 杀死被占用端口进程

		D:\Users\fraser.yu>taskkill -F -PID "1696"
		SUCCESS: The process with PID 1696 has been terminated.

以上就可以解决问题. 如果需要更多查看端口的功能查看命令**netstat -help**
