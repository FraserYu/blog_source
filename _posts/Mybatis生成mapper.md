layout: intellij
title: Mybatis生成mapper
date: 2016-08-29 20:17:33
tags:
	- mybatis-generator
	- mapper
	- mybatis
category: Mybatis
---

首先非常感谢[源博文](http://blog.csdn.net/liudongdong0909/article/details/51534735)的作者的奉献，我也将实际操作及在操作过程中遇到的问题说明在这里：

#### 数据库及表的建立
我用的是Mysql5.7，下面是新建数据库及表的SQL语句：
1. 新建数据库

		CREATE SCHEMA `frame` DEFAULT CHARACTER SET utf8 COLLATE utf8_bin ;

<!-- more -->

2. 新建数据库表

		CREATE TABLE `frame`.`f_user` (
		  `username` VARCHAR(40) NOT NULL,
		  `email` VARCHAR(100) NULL,
		  `password` VARCHAR(30) NOT NULL,
		  `create_time` DATETIME NULL DEFAULT CURRENT_TIMESTAMP,
		  `id` VARCHAR(30) NOT NULL,
		  PRIMARY KEY (`id`))
		ENGINE = MyISAM;

#### 准备
首先在src/main/resources/config 目录下新建名为**generatorConfig.xml**的文件
> + 目录可以是resources目录下的任意目录
> + generatorConfig.xml 也可以是其他名称（通常是这个）

以上两个文件都要在稍后进行配置说明

####  generatorConfig.xml 配置

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE generatorConfiguration
	        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
	        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

	<generatorConfiguration>
	    <!--mysql locate mysql connector jar in local disk-->
	    <classPathEntry location="D:/projects/myProjects/mysql-connector-java-5.1.39-bin.jar" />
	    <context id="testTables" targetRuntime="MyBatis3">
	        <commentGenerator>
	            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
	            <property name="suppressAllComments" value="true" />
	        </commentGenerator>
	        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
	        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
	                        connectionURL="jdbc:mysql://localhost:3306/frame" userId="root"
	                        password="000000">
	        </jdbcConnection>
	        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
	           NUMERIC 类型解析为java.math.BigDecimal -->
	        <javaTypeResolver>
	            <property name="forceBigDecimals" value="true" />
	        </javaTypeResolver>

	        <!-- targetProject:生成PO类的位置 -->
	        <javaModelGenerator targetPackage="com.demo.entity"
	                            targetProject="src/main/java">
	            <!-- enableSubPackages:是否让schema作为包的后缀 -->
	            <property name="enableSubPackages" value="false" />
	            <!-- 从数据库返回的值被清理前后的空格 -->
	            <property name="trimStrings" value="true" />
	        </javaModelGenerator>
	        <!-- targetProject:mapper映射文件生成的位置
	           如果maven工程只是单独的一个工程，targetProject="src/main/java"
	           若果maven工程是分模块的工程，targetProject="所属模块的名称"，例如：
	           targetProject="ecps-manager-mapper"，下同-->
	        <sqlMapGenerator targetPackage="com.demo.dao.mapingXML"
	                         targetProject="src/main/java">
	            <!-- enableSubPackages:是否让schema作为包的后缀 -->
	            <property name="enableSubPackages" value="false" />
	        </sqlMapGenerator>
	        <!-- targetPackage：mapper接口生成的位置 -->
	        <javaClientGenerator type="XMLMAPPER"
	                             targetPackage="com.demo.dao"
	                             targetProject="src/main/java">
	            <!-- enableSubPackages:是否让schema作为包的后缀 -->
	            <property name="enableSubPackages" value="false" />
	        </javaClientGenerator>
	        <!-- 指定数据库表 -->
	        <table schema="frame" tableName="f_user" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false" selectByExampleQueryId="false"/>
	    </context>
	</generatorConfiguration>

说明一下上述配置文件主要标签的意思
> + **classPathEntry ** 这个要指定mysql-connector-java-5.1.39-bin.jar，版本你自己决定
> + **javaModelGenerator ** 也就是生成POJO java类，对应数据库表明及字段
> + **sqlMapGenerator ** 对应*mapper.xml 文件，也就是SQL的语句在这里面
> + **javaClientGenerator ** mapper的生成，也就是生成Dao的接口
> + **table ** 这里要对应数据库的schema及数据库表明，同时后面如*enableCountByExample* 带有example的等于false，避免生成example数据，这样生成的文件看起来很整洁

#### pom.xml配置
接下来要把mybatis-generator相关的依赖包引入到pom.xml文件中，此文件包含了**generatorConfig.xml** 路径的配置，在**build**标签内加入以下内容：

	<build>
	      <finalName>springmvc</finalName>
	      <plugins>
	          <plugin>
	              <groupId>org.mybatis.generator</groupId>
	              <artifactId>mybatis-generator-maven-plugin</artifactId>
	              <version>1.3.2</version>
	              <configuration>
	                  <!--配置文件的位置-->
	                  <configurationFile>src/main/resources/config/generatorConfig.xml</configurationFile>
	                  <verbose>true</verbose>
	                  <overwrite>true</overwrite>
	              </configuration>
	              <executions>
	                  <execution>
	                      <id>Generate MyBatis Artifacts</id>
	                      <goals>
	                          <goal>generate</goal>
	                      </goals>
	                  </execution>
	              </executions>
	              <dependencies>
	                  <dependency>
	                      <groupId>org.mybatis.generator</groupId>
	                      <artifactId>mybatis-generator-core</artifactId>
	                      <version>1.3.2</version>
	                  </dependency>
	              </dependencies>
	          </plugin>
	      </plugins>
	  </build>

里面包含了以下两个依赖包：
> + **mybatis-generator-core**
> + **mybatis-generator-maven-plugin**

#### 对象配置
至此我们的主要配置都结束了，那要怎样将这些配置和项目关联起来，生成相关的POPO,mapper.xml及mapper 接口呢？ 接下来的一步将解决问题：

##### Step1 Edit Configurations
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/mybatisGenerator_mybatis1.png)

##### Step2 Add Maven
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/mybatisGenerator_mybatis2.png)

##### Step3 Run
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/mybatisGenerator_mybatis3.png)

##### Step4 查看目录结构
如下图所示，已经在相应的目录生成相应的文件：
![enter image description here](http://7xkyc7.com1.z0.glb.clouddn.com/mybatisGenerator_mybatis4.png)

##### 文件详细
1. FUserMapper.xml

		<?xml version="1.0" encoding="UTF-8"?>
		<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
		<mapper namespace="com.demo.dao.FUserMapper">
		  <resultMap id="BaseResultMap" type="com.demo.entity.FUser">
		    <id column="id" jdbcType="VARCHAR" property="id" />
		    <result column="username" jdbcType="VARCHAR" property="username" />
		    <result column="email" jdbcType="VARCHAR" property="email" />
		    <result column="password" jdbcType="VARCHAR" property="password" />
		    <result column="create_time" jdbcType="TIMESTAMP" property="createTime" />
		  </resultMap>
		  <sql id="Base_Column_List">
		    id, username, email, password, create_time
		  </sql>
		  <select id="selectByPrimaryKey" parameterType="java.lang.String" resultMap="BaseResultMap">
		    select
		    <include refid="Base_Column_List" />
		    from f_user
		    where id = #{id,jdbcType=VARCHAR}
		  </select>
		  <delete id="deleteByPrimaryKey" parameterType="java.lang.String">
		    delete from f_user
		    where id = #{id,jdbcType=VARCHAR}
		  </delete>
		  <insert id="insert" parameterType="com.demo.entity.FUser">
		    insert into f_user (id, username, email,
		      password, create_time)
		    values (#{id,jdbcType=VARCHAR}, #{username,jdbcType=VARCHAR}, #{email,jdbcType=VARCHAR},
		      #{password,jdbcType=VARCHAR}, #{createTime,jdbcType=TIMESTAMP})
		  </insert>
		  <insert id="insertSelective" parameterType="com.demo.entity.FUser">
		    insert into f_user
		    <trim prefix="(" suffix=")" suffixOverrides=",">
		      <if test="id != null">
		        id,
		      </if>
		      <if test="username != null">
		        username,
		      </if>
		      <if test="email != null">
		        email,
		      </if>
		      <if test="password != null">
		        password,
		      </if>
		      <if test="createTime != null">
		        create_time,
		      </if>
		    </trim>
		    <trim prefix="values (" suffix=")" suffixOverrides=",">
		      <if test="id != null">
		        #{id,jdbcType=VARCHAR},
		      </if>
		      <if test="username != null">
		        #{username,jdbcType=VARCHAR},
		      </if>
		      <if test="email != null">
		        #{email,jdbcType=VARCHAR},
		      </if>
		      <if test="password != null">
		        #{password,jdbcType=VARCHAR},
		      </if>
		      <if test="createTime != null">
		        #{createTime,jdbcType=TIMESTAMP},
		      </if>
		    </trim>
		  </insert>
		  <update id="updateByPrimaryKeySelective" parameterType="com.demo.entity.FUser">
		    update f_user
		    <set>
		      <if test="username != null">
		        username = #{username,jdbcType=VARCHAR},
		      </if>
		      <if test="email != null">
		        email = #{email,jdbcType=VARCHAR},
		      </if>
		      <if test="password != null">
		        password = #{password,jdbcType=VARCHAR},
		      </if>
		      <if test="createTime != null">
		        create_time = #{createTime,jdbcType=TIMESTAMP},
		      </if>
		    </set>
		    where id = #{id,jdbcType=VARCHAR}
		  </update>
		  <update id="updateByPrimaryKey" parameterType="com.demo.entity.FUser">
		    update f_user
		    set username = #{username,jdbcType=VARCHAR},
		      email = #{email,jdbcType=VARCHAR},
		      password = #{password,jdbcType=VARCHAR},
		      create_time = #{createTime,jdbcType=TIMESTAMP}
		    where id = #{id,jdbcType=VARCHAR}
		  </update>
		</mapper>

2. FUserMapper.java

		package com.demo.dao;

		import com.demo.entity.FUser;

		public interface FUserMapper {
		    int deleteByPrimaryKey(String id);

		    int insert(FUser record);

		    int insertSelective(FUser record);

		    FUser selectByPrimaryKey(String id);

		    int updateByPrimaryKeySelective(FUser record);

		    int updateByPrimaryKey(FUser record);
		}

3. FUser.java

		package com.demo.entity;

		import java.util.Date;

		public class FUser {
		    private String id;

		    private String username;

		    private String email;

		    private String password;

		    private Date createTime;

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
		}


以上如果遇到如没有“mybatis-generator plugin”这类的错误，运行一下maven的clean命令，然后重新执行，如果还是报错要重新检查一下配置文件，至此关于mybatis-generator的配置全部结束。
