---
title: maven include/exclude文件
tags:
  - Maven
  - Include
  - Exclude
category: 构建工具
abbrlink: 251ff325
date: 2016-08-31 21:29:36
---

一般情况下，我们用到的资源文件（各种xml，properites，xsd文件等）都放在src/main/resources下面，利用maven打包时，maven能把这些资源文件打包到相应的jar或者war里。

有时候，比如mybatis的mapper.xml文件，我们习惯把它和Mapper.java放一起，都在src/main/java下面，这样利用maven打包时，就需要修改pom.xml文件，来把mapper.xml文件一起打包进jar或者war里了，否则，这些文件不会被打包的。（maven认为src/main/java只是java的源代码路径）。网络上有很多方法，我大概试了下，几种方法都可以，可以任选一种即可。

<!-- more -->

1. **方法一：**其中**/*这样的写法，是为了保证各级子目录下的资源文件被打包

		<build>
		    <finalName>test</finalName>
		    <!--
		    这样也可以把所有的xml文件，打包到相应位置。
		    <resources>
		        <resource>
		            <directory>src/main/resources</directory>
		            <includes>
		                <include>**/*.properties</include>
		                <include>**/*.xml</include>
		                <include>**/*.tld</include>
		            </includes>
		            <filtering>false</filtering><--这里是false，用true会报 数据库连接 错误-->
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
		</build>
其中**/*这样的写法，是为了保证各级子目录下的资源文件被打包

2. **方法二：**利用build-helper-maven-plugin插件

		<build>
		    ...
		    </plugins>
		        ...
		        <!--
		        此plugin可以用
		        利用此plugin，把源代码中的xml文件，
		        打包到相应位置，这里主要是为了打包Mybatis的mapper.xml文件
		        -->
		        <plugin>
		            <groupId>org.codehaus.mojo</groupId>
		            <artifactId>build-helper-maven-plugin</artifactId>
		            <version>1.8</version>
		            <executions>
		                <execution>
		                    <id>add-resource</id>
		                    <phase>generate-resources</phase>
		                    <goals>
		                        <goal>add-resource</goal>
		                    </goals>
		                    <configuration>
		                        <resources>
		                            <resource>
		                                <directory>src/main/java</directory>
		                                <includes>
		                                    <include>**/*.xml</include>
		                                </includes>
		                            </resource>
		                        </resources>
		                    </configuration>
		                </execution>
		            </executions>
		        </plugin>   
		        ...
		    </plugins>     
		    ...
		</build>

3. **方法三：**  利用maven-resources-plugin插件

		<build>
		    ...
		    </plugins>
		        ...
		        <!--
		        此plugin可以用
		        利用此plugin，把源代码中的xml文件，打包到相应位置，
		        这里主要是为了打包Mybatis的mapper.xml文件
		        -->
		        <plugin>
		            <artifactId>maven-resources-plugin</artifactId>
		            <version>2.5</version>
		            <executions>
		                <execution>
		                    <id>copy-xmls</id>
		                    <phase>process-sources</phase>
		                    <goals>
		                        <goal>copy-resources</goal>
		                    </goals>
		                    <configuration>
		                        <outputDirectory>${basedir}/target/classes</outputDirectory>
		                        <resources>
		                            <resource>
		                                <directory>${basedir}/src/main/java</directory>
		                                <includes>
		                                    <include>**/*.xml</include>
		                                </includes>
		                            </resource>
		                        </resources>
		                    </configuration>
		                </execution>
		            </executions>
		        </plugin>   
		        ...
		    </plugins>     
		    ...
		</build>

感谢[原文](http://www.cnblogs.com/oyx305/p/5340683.html)的作者
