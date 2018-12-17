---
title: java_web基础知识
date: 2017-02-27 13:13:47
categories: 
- 基础
tags:
- 基础
- java
---

## java_web基础知识总结

##### url编码问题
- 当url请求中包含中文时，需要对中文字符进行url编码，java自带api函数URLDecoder.decode（解码）,URLEncoder.encode（编码）可进行url的编码解码操作。

##### 

- 启动WEB项目的时候，会读取web.xml，读取顺序content-param --> listener --> filter --> servlet

