---
title: 修改Dynamic Web Module 报错
tags: Eclipse
category: Java
abbrlink: c72016f5
date: 2016-01-21 22:41:10
---
# 修改Dynamic Web Module 报错
-----------------------------------------------
当我们在project facet修改Dynamic Web Module的时候可能会出现**Cannot change version of project facet Dynamic Web Module to 3.0**, 当然也可能是其他版本（2.5），
我们需要修改workspace中项目.settings.xml目录下的
org.eclipse.wst.common.project.facet.core.xml, 修改jst.web的version即可。
```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <faceted-project>
      <fixed facet="wst.jsdt.web"/>
      <installed facet="jst.web" version="3.0"/>
      <installed facet="wst.jsdt.web" version="1.0"/>
      <installed facet="java" version="1.7"/>
    </faceted-project>
```
