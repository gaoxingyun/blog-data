---
title: maven使用
date: 2017-06-23 09:12:31
categories: 
- 工具
tags:
- 工具
- java
---


#### 添加jar包到maven本地仓库

- 添加jar包到maven本地仓库
```
mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=9.0.2.0.0 -Dpackaging=jar -Dfile=ojdbc14.jar
```

#### 跳过单元测试

- 
```
mvn package -Dmaven.test.skip=true
```


#### 发布项目到私有仓库

1. 在项目Pom.xml中<project></project>之间加入
```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <name>Popocloud Release Repository</name>
        <url>http://maven:8081/nexus/content/repositories/releases/</url>
     </repository>
    <snapshotRepository>
        <id>snapshots</id>
        <name>Popocloud Snapshot Repository</name>
        <url>http://maven:8081/nexus/content/repositories/snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

2. 确保maven.setting.xml中加入了私有仓库用户名和密码
```xml
<server>  
   <id>releases</id>  
   <username>admin</username>  
   <password>admin123</password>  
 </server>  
 
 <server>
      <id>snapshots</id>
      <username>admin</username>
      <password>admin123</password>
  </server>
</servers>
```

3. 对项目执行maven deploy命令，可实现将项目发布的maven私有仓库供其他开发人员使用。
```
mvn deploy:deploy-file -Dmaven.test.skip=true -Dfile=alipay-sdk-java20151021120052-1.0.jar -DgroupId=alipay -DartifactId=alipay-sdk-java20151021120052 -Dversion=1.0 -Dpackaging=jar -DrepositoryId=nexus-thirdparty -Durl=http://192.168.13.145:10081/nexus/content/repositories/thirdparty/


```

#### maven依赖管理

1. 第一声明优先原则
示例：

```xml
<dependencies>
<!--   spring-beans-4.2.4 -->
  	<dependency>
  		<groupId>org.springframework</groupId>
  		<artifactId>spring-context</artifactId>
  		<version>4.2.4.RELEASE</version>
  	</dependency>
  
<!--   spring-beans-3.0.5 -->
  	<dependency>
  		<groupId>org.apache.struts</groupId>
  		<artifactId>struts2-spring-plugin</artifactId>
  		<version>2.3.24</version>
  	</dependency>
```

2. 路径近者优先原则
示例：

```xml
<dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-beans</artifactId>
  	<version>4.2.4.RELEASE</version>
</dependency>
```


3. 排除原则
示例：

```xml
<dependency>
  	<groupId>org.apache.struts</groupId>
  	<artifactId>struts2-spring-plugin</artifactId>
  	<version>2.3.24</version>
  	<exclusions>
  	   <exclusion>
  	      <groupId>org.springframework</groupId>
  	      <artifactId>spring-beans</artifactId>
  	   </exclusion>
       </exclusions>
</dependency>
```

4. 版本锁定原则
示例：

```xml
<properties>
	<spring.version>4.2.4.RELEASE</spring.version>
	<hibernate.version>5.0.7.Final</hibernate.version>
	<struts.version>2.3.24</struts.version>
</properties>
 
<!-- 锁定版本，struts2-2.3.24、spring4.2.4、hibernate5.0.7 -->
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
		</dependency>
        </dependencies>
</dependencyManagement>
```


##### 依赖调解

当依赖冲突时，maven依照以下原则自动进行选择优先的依赖版本

- 依赖调解第一原则：路径优先，优先路径深度最短的版本
- 依赖调解第二原则：声明优先，假设路径深度相等，那么声明在前的会被引用。

##### 查看依赖树

- 可执行以下命令查看依赖数，\-表示被忽略掉的依赖

```
mvn dependency:tree


[INFO] +- junit:junit:jar:4.12:compile
[INFO] |  \- org.hamcrest:hamcrest-core:jar:1.3:compile
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:1.4.2.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:1.4.2.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-starter-logging:jar:1.4.2.RELEASE:compile
[INFO] |  |  |  +- ch.qos.logback:logback-classic:jar:1.1.7:compile
[INFO] |  |  |  |  \- ch.qos.logback:logback-core:jar:1.1.7:compile
[INFO] |  |  |  +- org.slf4j:jul-to-slf4j:jar:1.7.21:compile
[INFO] |  |  |  \- org.slf4j:log4j-over-slf4j:jar:1.7.21:compile
[INFO] |  |  +- org.springframework:spring-core:jar:4.3.4.RELEASE:compile
[INFO] |  |  \- org.yaml:snakeyaml:jar:1.17:runtime
[INFO] |  +- org.springframework.boot:spring-boot-starter-tomcat:jar:1.4.2.RELEASE:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:8.5.6:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-el:jar:8.5.6:compile
[INFO] |  |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:8.5.6:compile
[INFO] |  +- org.hibernate:hibernate-validator:jar:5.2.4.Final:compile
[INFO] |  |  +- javax.validation:validation-api:jar:1.1.0.Final:compile
[INFO] |  |  +- org.jboss.logging:jboss-logging:jar:3.3.0.Final:compile
[INFO] |  |  \- com.fasterxml:classmate:jar:1.3.3:compile
[INFO] |  +- org.springframework:spring-web:jar:4.3.4.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-aop:jar:4.3.4.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-beans:jar:4.3.4.RELEASE:compile
[INFO] |  |  \- org.springframework:spring-context:jar:4.3.4.RELEASE:compile
[INFO] |  \- org.springframework:spring-webmvc:jar:4.3.4.RELEASE:compile
[INFO] |     \- org.springframework:spring-expression:jar:4.3.4.RELEASE:compile
[INFO] +- org.springframework.boot:spring-boot-starter-data-jpa:jar:1.4.2.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-aop:jar:1.4.2.RELEASE:compile
[INFO] |  |  \- org.aspectj:aspectjweaver:jar:1.8.9:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-jdbc:jar:1.4.2.RELEASE:compile
[INFO] |  |  +- org.apache.tomcat:tomcat-jdbc:jar:8.5.6:compile
[INFO] |  |  |  \- org.apache.tomcat:tomcat-juli:jar:8.5.6:compile
[INFO] |  |  \- org.springframework:spring-jdbc:jar:4.3.4.RELEASE:compile
[INFO] |  +- org.hibernate:hibernate-core:jar:5.0.11.Final:compile
[INFO] |  |  +- org.hibernate.javax.persistence:hibernate-jpa-2.1-api:jar:1.0.0.Final:compile
[INFO] |  |  +- org.javassist:javassist:jar:3.20.0-GA:compile
[INFO] |  |  +- antlr:antlr:jar:2.7.7:compile
[INFO] |  |  +- org.jboss:jandex:jar:2.0.0.Final:compile
[INFO] |  |  +- dom4j:dom4j:jar:1.6.1:compile
[INFO] |  |  |  \- xml-apis:xml-apis:jar:1.4.01:compile
[INFO] |  |  \- org.hibernate.common:hibernate-commons-annotations:jar:5.0.1.Final:compile
[INFO] |  +- org.hibernate:hibernate-entitymanager:jar:5.0.11.Final:compile
[INFO] |  +- javax.transaction:javax.transaction-api:jar:1.2:compile
[INFO] |  +- org.springframework.data:spring-data-jpa:jar:1.10.5.RELEASE:compile
[INFO] |  |  +- org.springframework.data:spring-data-commons:jar:1.12.5.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-orm:jar:4.3.4.RELEASE:compile
[INFO] |  |  +- org.springframework:spring-tx:jar:4.3.4.RELEASE:compile
[INFO] |  |  +- org.slf4j:slf4j-api:jar:1.7.21:compile
[INFO] |  |  \- org.slf4j:jcl-over-slf4j:jar:1.7.21:compile
[INFO] |  \- org.springframework:spring-aspects:jar:4.3.4.RELEASE:compile
[INFO] +- org.springframework.boot:spring-boot-starter-actuator:jar:1.4.2.RELEASE:compile
[INFO] |  \- org.springframework.boot:spring-boot-actuator:jar:1.4.2.RELEASE:compile
[INFO] +- org.springframework.boot:spring-boot-devtools:jar:1.4.2.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot:jar:1.4.2.RELEASE:compile
[INFO] |  \- org.springframework.boot:spring-boot-autoconfigure:jar:1.4.2.RELEASE:compile
[INFO] +- com.fasterxml.jackson.dataformat:jackson-dataformat-xml:jar:2.8.4:compile
[INFO] |  +- com.fasterxml.jackson.module:jackson-module-jaxb-annotations:jar:2.8.4:compile
[INFO] |  +- org.codehaus.woodstox:stax2-api:jar:3.1.4:compile
[INFO] |  \- com.fasterxml.woodstox:woodstox-core:jar:5.0.2:compile
[INFO] +- com.fasterxml.jackson.core:jackson-core:jar:2.8.4:compile
[INFO] +- com.fasterxml.jackson.core:jackson-annotations:jar:2.8.4:compile
[INFO] +- com.fasterxml.jackson.core:jackson-databind:jar:2.8.4:compile
[INFO] +- org.apache.axis:axis:jar:1.4:compile
[INFO] +- commons-discovery:commons-discovery:jar:0.2:compile
[INFO] +- javax.xml:jaxrpc-api:jar:1.1:compile
[INFO] +- org.eclipse.birt.runtime.3_7_1:javax.wsdl:jar:1.5.1:compile
[INFO] +- commons-logging:commons-logging:jar:1.1.1:compile
[INFO] +- org.apache.logging.log4j:log4j-api:jar:2.6.2:compile
[INFO] +- org.apache.logging.log4j:log4j-core:jar:2.6.2:compile
[INFO] +- mysql:mysql-connector-java:jar:5.1.40:compile
[INFO] +- com.oracle:ojdbc6:jar:11.2.0.3:compile
[INFO] +- com.h2database:h2:jar:1.4.192:compile
[INFO] \- com.alibaba:druid:jar:1.0.26:compile
[INFO]    +- com.alibaba:jconsole:jar:1.8.0:system
[INFO]    \- com.alibaba:tools:jar:1.8.0:system
```

#### 问题

- eclipse中的maven web项目报错：org/codehaus/plexus/archiver/jar/JarArchiver[http://blog.csdn.net/lele2426/article/details/38963071](http://blog.csdn.net/lele2426/article/details/38963071)

#### 名词

- 一方库：本工程中的各模块的相互依赖
- 二方库：公司内部的依赖库，一般指公司内部的其他项目发布的jar包
- 三方库：公司之外的开源库， 比如apache、ibm、google等发布的依赖