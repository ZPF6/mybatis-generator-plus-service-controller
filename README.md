

这个项目在gitee上面维护。

https://gitee.com/fufu669/mybatis-generator-plus-service-controller



<h1>mybatis-generator-plus-service-controller</h1>
<h6>一个自动生成Service(CRUD,Query, List,Distinct Select,Batch Add)，Controller，HTML(前台前端使用vue框架，后台前端使用jquery)的生成器。</h6>
<hr>

使用说明：

**1. 引入pom.xml**
```
<dependency>
  <groupId>com.gitee.fufu669</groupId>
  <artifactId>mybatis-generator-plus-service-controller</artifactId>
  <version>最新版本</version>
</dependency>
```
**2. 创建一个maven的springboot程序**
```
如果没有程序，参考我之前的样例。拷贝覆盖下面的这个pom.xml。拷贝覆盖generatorConfig.xml进入src/main/resources文件夹。
```
**3. 在intellij中运行这个maven命令。**
```
-Dfile.encoding=UTF-8 -Dmybatis.generator.overwrite=true mybatis-generator:generate -e -X
就能生成全量的service，controller，html的code。
```

**4.约定**
```
(如果不用HTML的管理后台则不用。)

sql数据库中有2张表：
CREATE TABLE `admin` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '管理员名称',
  `password` varchar(50) NOT NULL COMMENT '密码',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `update_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`),
  KEY `password` (`password`(32))
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8


CREATE TABLE `select_config` (
  `config_key` varchar(100) NOT NULL DEFAULT '',
  `config_value` varchar(500) NOT NULL DEFAULT '',
  PRIMARY KEY (`config_key`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```

**5.pom.xml**
````
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	
	<modelVersion>4.0.0</modelVersion>
	
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.7.RELEASE</version>
	</parent>
	<groupId>com.fupengwang.soccer.shopping.server</groupId>
	<artifactId>fupengwang-recommand-server</artifactId>
	<version>66601</version>
	<packaging>jar</packaging>
	<name>billphones-rt-job-fupengwang.recommand-server</name>
	<description>billphones-rt-job-fupengwang-recommand-server</description>
	<properties>
	</properties>
	<dependencies>
	    <!-- 在这有你自己的依赖 -->
	</dependencies>
	<profiles>
		<profile>
			<id>dev</id>
			<properties>
				<env>dev</env>
			</properties>
			<activation>
				<activeByDefault>true</activeByDefault>
			</activation>
		</profile>
	</profiles>
	<build>
		<plugins>
			<plugin>
				<groupId>org.mybatis.generator</groupId>
				<artifactId>mybatis-generator-maven-plugin</artifactId>
				<version>1.3.5</version>
				<configuration>
					<configurationFile>
						src/main/resources/generatorConfig.xml
					</configurationFile>
				</configuration>
				<dependencies>
					<dependency>
						<groupId>mysql</groupId>
						<artifactId>mysql-connector-java</artifactId>
						<version>5.1.38</version>
					</dependency>
					<dependency>
						<groupId>org.mybatis.generator.plus</groupId>
                        <artifactId>mybatis-generator-plus-service-controller</artifactId>
                        <version>6.6.686</version>
					</dependency>
				</dependencies>
			</plugin>
		</plugins>
	</build>
</project>
````


**6.generatorConfig.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<!--
maven cmd:
-Dfile.encoding=UTF-8 -Dmybatis.generator.overwrite=true mybatis-generator:generate -e -X
 -->

<generatorConfiguration>
	<context id="fpfpp" targetRuntime="MyBatis3" defaultModelType="flat">
		<plugin type="org.mybatis.generator.api.plus.MybatisPluginComment"></plugin>
		<plugin type="org.mybatis.generator.api.plus.MybatisPluginAdditionalWhere"></plugin>
		<plugin type="org.mybatis.generator.api.plus.MybatisPlugin"></plugin>
		<plugin type="org.mybatis.generator.api.plus.MybatisPluginSerializable"></plugin>
		<plugin type="org.mybatis.generator.api.plus.MybatisPluginEqualsHashCode"></plugin>
		
		<plugin type="org.mybatis.generator.api.plus.MybatisPluginService">
            <!--包的基础路径-->
            <property name="basePackage" value="com.fupengwang.hunter.back.server" />
            <!--注解所在包名, 常用的如@AuthUser所在路径-->
			<property name="aspectPackage" value="com.fupengwang.hunter.back.server.aspect" />
            <!--service所在包名，会在这个包里面生成service方法-->
			<property name="targetPackage" value="com.fupengwang.hunter.back.server.service.common" />
            <!--controller所在包名，给distinctController传递路径所用-->
			<property name="controllerPackage" value="com.fupengwang.hunter.back.server.controller.common" />
            <!--基础路径所在硬盘的位置-->
			<property name="targetProject" value="./src/main/java" />
            <!--是否创建基础的feign客户端，和对应的wrapper-->
            <property name="createFeign" value="false" />
            <!--feign客户端里调用的@FeignClient(name = "")里的name的值-->
			<property name="eurekaServiceName" value="com.fupengwang.hunter.back.server" />
            <!--如果feignClientUrl留空则不会写入@FeignClient(name = "zzzzzz", url = "zzzzzz")里面的url值-->
			<property name="feignClientUrl" value="" />
            <!--如果true，会自动创建distinct的mapper，service，controller-->
			<property name="createDistinct" value="true" />
            <!--如果true，会自动创建batch的mapper，service，用于插入一个队列的数据进入数据库-->
			<property name="createBatch" value="true" />
		</plugin>

        <plugin type="org.mybatis.generator.api.plus.MybatisPluginController">
            <property name="basePackage" value="com.fupengwang.hunter.back.server" />
            <property name="servicePackage" value="com.fupengwang.hunter.back.server.service.common" />
            <property name="controllerPackage" value="com.fupengwang.hunter.back.server.controller.common" />
            <property name="aspectPackage" value="com.fupengwang.hunter.back.server.aspect" />
            <property name="targetProject" value="./src/main/java" />
		</plugin>

        <plugin type="org.mybatis.generator.api.plus.MybatisPluginHtmlBackEnd">
            <property name="basePackage" value="com.fupengwang.support.server" />
            <property name="htmlPath" value="./src/main/webapp" />
            <property name="serverUrl" value="http://118.25.110.149:10666" />
            <property name="loginUrl" value="http://118.25.110.149:10666/v1/common/admin/login" />
            <property name="tokenErrorStatus" value="666125" />
            <property name="fileServer" value="http://118.25.176.245:10666/v1/image" />
        </plugin>

        <plugin type="org.mybatis.generator.api.plus.MybatisPluginHtmlFrontEnd">
            <property name="basePackage" value="com.fupengwang.support.server" />
            <property name="htmlPath" value="./src/main/webappfront" />
            <property name="serverUrl" value="http://www.fupengwang.com/app" />
            <property name="loginUrl" value="http://www.fupengwang.com/app/v2/shopping/soccer/login" />
            <property name="tokenErrorStatus" value="666125" />
            <property name="fileServer" value="http://www.fupengwang.com/tfs/v1/image" />
        </plugin>

		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
            connectionURL="jdbc:mysql://cdb-19mh3aau.bj.tencentcdb.com:10024/fupengwang_clothes" userId="root"
            password="Fff66">
		</jdbcConnection>

		<javaTypeResolver>
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>

		<javaModelGenerator targetPackage="com.fupengwang.front.server.clothes.bean.model"
			targetProject="./src/main/java">
			<property name="enableSubPackages" value="false" />
			<property name="trimStrings" value="true" />
		</javaModelGenerator>

		<sqlMapGenerator targetPackage="com.fupengwang.front.server.clothes.bean.mapper"
			targetProject="./src/main/resources">
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>

		<javaClientGenerator
			targetPackage="com.fupengwang.front.server.clothes.bean.mapper"
			targetProject="./src/main/java" type="XMLMAPPER">
			<property name="enableSubPackages" value="true" />
			<property name="targetServicePackage"
				value="com.fupengwang.front.server.clothes.Service" />
			<property name="targetServiceProject" value="./src/main/java" />
		</javaClientGenerator>

		<!--table start here, please add your table below-->


		<table tableName="admin" domainObjectName="Admin" enableInsert="true"
			   enableSelectByPrimaryKey="true" enableSelectByExample="true"
			   enableUpdateByPrimaryKey="true" enableDeleteByPrimaryKey="true"
			   enableDeleteByExample="true" enableCountByExample="true"
			   enableUpdateByExample="true" selectByPrimaryKeyQueryId="true"
			   selectByExampleQueryId="true">
			<generatedKey column="id" sqlStatement="mysql" identity="true" />
		</table>

		<table tableName="select_config" domainObjectName="SelectConfig" enableInsert="true"
			   enableSelectByPrimaryKey="true" enableSelectByExample="true"
			   enableUpdateByPrimaryKey="true" enableDeleteByPrimaryKey="true"
			   enableDeleteByExample="true" enableCountByExample="true"
			   enableUpdateByExample="true" selectByPrimaryKeyQueryId="true"
			   selectByExampleQueryId="true">
			<generatedKey column="id" sqlStatement="mysql" identity="true" />
		</table>

	</context>
	<!-- 
	maven cmd: 
	-Dfile.encoding=UTF-8 -Dmybatis.generator.overwrite=true mybatis-generator:generate -e -X
	 -->
	 
</generatorConfiguration>

```
**7.在pom里面加上这个依赖**
```
@CacheFetchLater功能需要。

<dependency>
    <groupId>com.gitee.fufu669</groupId>
	<artifactId>cache-fetch-later</artifactId>
	<version>最新版本详见网页https://gitee.com/fufu669/cache-fetch-later</version>
</dependency>
```
**8.缓存框架用法请参照**
```
用法请参考：https://gitee.com/fufu669/cache-fetch-later
```
完成了。

**5. 更改日志**
```
6.6.685 删除了创建SimpleResponse，现在SimpleResponse从CacheFetchLater包里面获取了。
6.6.686 增加equals方法中用instanceof方法判断type是否一致。消除sonar中的type test错误。
6.6.687 增加createDisinct配置，如果配置成false就不生成对应的代码. 增加createBatch配置，如果配置成false就不生成对应的代码.
6.6.688 把distinctController独立出来
6.6.6603 增加了returnType的选项，使得如果returnType=AlphaSimpleResponse，则生成的是AlphaSimpleResponse，否则生成SimpleResponse
```
