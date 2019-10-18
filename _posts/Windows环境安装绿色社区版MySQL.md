---
title: Windows环境安装绿色社区版MySQL
category: MySQL
tags:
  - 绿色版MySQL
abbrlink: 4a85d28c
date: 2018-03-28 14:38:47
---

<blockquote class="blockquote-center">探寻MySQL之安装</blockquote>

### 前言
以下篇幅按正常流程讲解下载、安装，启动服务，登录等，因为安装环境等个体差异可能导致安装过程中遇到一些问题，我在安装过程中同样遇到了一些问题，我将<a href="#qa">问题汇总</a>在最后，如遇到相同问题可以作为参考.

### 下载 MySQL

打开[MySQl Download](https://www.mysql.com/downloads/),页面下拉到最下方如下图
<img itemprop="url image" src="/uploads/MySQL/mysql_download.png" />

<!--more-->
点击 **Community (GPL) Downloads »** 链接如下图：
<img itemprop="url image" src="/uploads/MySQL/mysql_download1.png"/>
继续点击 **DOWNLOAD** 链接如下图：
<img itemprop="url image" src="/uploads/MySQL/mysql_download2.png" />
按照自己的系统选择需要下载的版本， 此处我选的的是 Windows 版本：mysql-5.7.21-winx64.zip

### 安装MySQL
1. 解压MySQL压缩包
解压到指定目录：D:\Tools\mysql-5.7.21-winx64
2. 添加环境变量
在Path中添加 D:\Tools\mysql-5.7.21-winx64\bin
3. 在 bin 的**同级目录** 下新建文件 **my.ini**, 内容如下：
```mysql
[mysqld]
# 设置MySQL的安装目录
basedir=D:\Tools\mysql-5.7.21-winx64
# 设置MySQL数据库数据的存放目录
datadir=D:\Tools\mysql-5.7.21-winx64\data
```
4. 安装MySQL服务
以管理员身份运行cmd.exe，然后进入到MySQL的bin目录, **如果安装目录下存在“Data”目录，务必先删除data目录(或移动到其他地方)**
```shell
cd D:\Tools\mysql-5.7.21-winx64\bin
mysqld --initialize
mysqld --install MySQL --defaults-file="D:\Tools\mysql-5.7.21-winx64\my.ini"
```
如果显示 **service successfully installed** 说明安装成功, 这个瞬间很快.

至此，MySQL已经安装成功，接下来就要启动MySQL服务了.

### MySQL服务
1. 启动与停止
+ 在 bin 目录下执行如下命令
```mysql
net start mysql
net stop mysql
```
+ 打开管理工具服务，找到MySQL服务， 选择启动与停止
2. 卸载MySQL
在bin目录下执行任意一个命令
```mysql
mysqld -remove
sc delete mysql
```

### root用户登录与密码修改
启动MySQL服务后，可以用root用户进行登录与密码修改，因为安装过程中root用户的密码默认为空，输入命令：
```mysql
mysql -u root -p
```
会提示输入密码，因为默认密码为空，直接敲击回车登录就好, 此处有可能遇到
> ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)错误

此时需要编辑 my.ini 文件，添加 **skip-grant-tables**，文件内容如下：
```mysql
[mysqld]
skip-grant-tables
basedir=D:\Tools\mysql-5.7.21-winx64
datadir=D:\Tools\mysql-5.7.21-winx64\data
```
保存退出后，重新启动MySQL服务，我们可以通过第三方工具Navicat for MySQL 或者 SQL语句来修改root的密码,下面是SQL语句方式修改密码：
```mysql
c:>mysql -u root -p
mysql>show databases;
mysql>use mysql;
mysql>UPDATE user SET authentication_string=PASSWORD("123456") WHERE user='root';
mysql>FLUSH PRIVILEGES; **注意一定不要遗忘这句话，否则密码更改不能生效，刷新MySQL的系统权限相关表，第二种方法，就是重新启动mysql服务器**
mysql>QUIT
```
改好之后，再修改一下my.ini这个文件，把刚才加入的"skip-grant-tables"这行删除，保存退出，再重启mysql。

<h3 id="qa"> 问题汇总</h3>
1. The service could not be started [参考](http://colitas.iteye.com/blog/816595)
> + 在开始-->>运行中输入services.msc, 查看是否存在mysql的service,如果存在，并且不能手动启动
> + 在开始-->>运行中输入regedit, 打开注册表，定位到 “HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services” , 删掉mysql对应的service;
> + 卸载 mysql(sc delete mysql),  重新启动系统;
> + 删除mysql的安装文件夹，并且删除mysql的数据存储文件, 重新启动系统;
> + 重新按照上面安装教程进行配置

2. ​The service already exists [参考](https://www.cnblogs.com/dichters/p/5929209.html)
这种问题的出现通常是之前安装过 MySQL，卸载后，服务没删掉.
> + 查询是否有mysql服务 （sc query mysql）
> + 如果有，执行 （sc delete sql）
> + 重新按照上面安装教程进行配置

### MySQL 常用命令

| 序号      |     语句 |   解释   |
| :-------- | :--------| :------ |
| 1    |   create database name; |  创建数据库  |
| 2    |   use databasename; |  选择数据库  |
| 3    |   drop database name; |  直接删除数据库，不提醒  |
| 4    |   show tables; |  显示表  |
| 5    |   describe tablename; |  表的详细描述  |
| 6    |   mysqladmin drop databasename |  删除数据库前，有提示  |
| 7    |   select version(),current_date; |  显示当前mysql版本和当前日期  |
| 8    |   mysql -h myhost -u root -p database < sql.txt |  从文件中读取  |

### 参考资料
1. https://www.cnblogs.com/weixiao520/p/4573619.html
2. https://www.cnblogs.com/dichters/p/5929209.html
3. http://colitas.iteye.com/blog/816595

　　

　　

　　
