---
title: Arthas调整log日志等级
categories: 工具
tags:
  - Arthas
  - Level
abbrlink: 7f0c2279
date: 2019-04-09 13:39:37
---

阿里的开源Java诊断工具Arthas可以在线调整log等级

1. 查找当前类的classloader hashcode

```shell
sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash
```



2. 用OGNL获取logger

```shell
ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger'
```



3. 单独设置UserController的logger level

```shell
ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger.setLevel(@ch.qos.logback.classic.Level@DEBUG)'
```



4. 全局设置logger level

```shell
ognl -c 1be6f5c3 '@org.slf4j.LoggerFactory@getLogger("root").setLevel(@ch.qos.logback.classic.Level@DEBUG)'
```
