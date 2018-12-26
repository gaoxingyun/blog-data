---
title: mycat使用
date: 2018-05-10 12:11:12
categories:
- 工具
tags:
- 工具
---


## mycat使用

#### 官网

- [http://www.mycat.io](http://www.mycat.io)
- [http://www.mycat.io/document/Mycat_V1.6.0.pdf](http://www.mycat.io/document/Mycat_V1.6.0.pdf)
- [https://github.com/MyCATApache/Mycat-Server](https://github.com/MyCATApache/Mycat-Server)


#### mycat-server安装配置
- 打开地址[http://dl.mycat.io](http://dl.mycat.io)，下载对应版本的mycat-server安装文件，解压。修改配置server.xml，
```xml
<!-- TESTDB为mycat对外提供的db名称，此db名称需与schema.xml中<schema name="TESTDB">对应 -->
<user name="root">
                <property name="password">123456</property>
                <property name="schemas">TESTDB</property>
</user>
```
schema.xml配置
```xml
<mycat:schema xmlns:mycat="http://io.mycat/">

        <schema name="TESTDB" checkSQLschema="false" sqlMaxLimit="100" dataNode="dn1">
        </schema>
        <!-- database为实体数据库中的数据库实例名 -->
        <dataNode name="dn1" dataHost="localhost1" database="testdb" />

        <dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"
                          writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
                <heartbeat>select user()</heartbeat>

<!-- writeHost和readHost对应实体数据库连接的配置，以下配置是一个简单的读写分离, 数据库的主从配置需数据库自行配置 -->                
                <writeHost host="hostM1" url="localhost:3306" user="root"
                                   password="123456">
                        <readHost host="hostS2" url="localhost:13306" user="root" password="123456" />
                </writeHost>
        </dataHost>
</mycat:schema>
```
配置完成后，执行`mycat start`,即可启动mycat服务。使用数据库工具连接8066端口，即可像使用普通mysql数据库一样使用mycat。


#### mycat-eye安装配置

- 打开地址[http://dl.mycat.io](http://dl.mycat.io)，下载对应版本的mycat-web安装文件，解压。修改配置mycat-web/mycat-web/WEB-INF/classes/mycat.properties，修改其中的zookeeper为自己的zookeeper服务地址。执行start.sh即可启动mycat-eye，登陆$SERVER_IP:8082/mycat，即可打开mycat-eye管理页面。
- sql统计功能，需在mycat配置文件server.xml中开启sqlStat功能。

## 问题

- 分库分表后，多个数据库中是允许存在相同的id的，如果业务不允许，需使用分布式全局唯一ID，或者使用id进行分片

#### 分库分表问题

垂直拆分带来的问题：

- 部分业务表无法join，只能通过接口方式，提高了系统复杂度
- 存在单表性能瓶颈，不易扩展 1000w 10张表 1亿
- 事务处理复杂

水平拆分带来的问题

- 拆分规则难以抽象
- 分片事务一致性难以解决
- 维护难度变大
- 跨库join性能差

共同问题

- 分片规则和策略
- 分布式全局唯一ID
- 多数据源管理问题 多个datasource
- 跨库跨表join问题
- 跨节点合并排序分页问题
- 事务复杂，带来了分布式事务问题
- 数据管理难度加大，出问题不好定位

#### 分库分表拆分原则

共同问题：

- 能不拆分尽量不拆分
- 如果要拆分一定要选择合适的拆分规则，提前规划好
- 数据拆分尽量通过数据冗余或表分组来降低跨库join的问题
- 跨库join是共同难题，所以业务读取尽量少使用多表join
- 真的需要分库分表吗？