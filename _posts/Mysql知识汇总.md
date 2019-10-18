---
title: Mysql知识汇总
tags:
  - MySQL
  - safe mode
category: MySQL
abbrlink: 7bf2540
date: 2016-11-29 09:49:53
---

在使用Mysql遇到的问题陆续会总结在此

| 错误描述     |     翻译 |
| :-------- | :--------|
|  Error Code: 1175. You are using safe update mode and you tried to update a table 》without a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences -> SQL Editor and reconnect.	0.016 sec.   | 在使用mysql执行update的时候，如果不是用主键当where语句，会报如下错误，使用主键用于where语句中正常|
**解决方案：**  取消安全模式

	SET SQL_SAFE_UPDATES = 0;

如果想恢复安全模式：

	SET SQL_SAFE_UPDATES = 1;
