---
title: java异步编程总结
date: 2018-01-09 09:25:54
categories: 
- 总结
tags:
- 总结
- 异步
- java
---


#

##

###### 常用库

- [rxJava](https://github.com/ReactiveX/RxJava) 安卓上流行
- [projectreactor](https://github.com/reactor/reactor-core) spring默认实现
- [vert.x](http://vertx.io/)



###### 名词

- Callback Hell 回调地狱
- 背压 背压是指在异步场景中，被观察者发送事件速度远快于观察者的处理速度的情况下，一种告诉上游的被观察者降低发送速度的策略。简而言之，背压是流速控制的一种策略。

## 面向对象开发原则
- SRP单一职责原则（Single Responsibility Principle）： 
　　就一个类而言，应该仅有一个引起它变化的原因。 
- OCP开放-封闭原则（Open Closure Principle）： 
　　软件实体（类，模块，函数等）应该可以扩展的，但不可修改。 
- LSPLiskov 替换原则（Liskov Substitution Principle）： 
　　子类型必须能够替换它们的基类型 
- DIP依赖倒置原则（Dependence Inversion Principle）： 
　　抽象不应该依赖于细节，细节应该依赖于抽象。 
- ISP接口隔离原则（Interface Segregation Principle）： 
　　不应该强迫客户依赖与它们不用的方法。接口属于客户，不属于它所在的类层次结构。 
- REP重用发布等价原则（Release Reuse Equivalency Principle）： 
　　重用粒度就是发布粒度。 
- CCP共同封闭原则（Common Closure Principle）： 
　　包中 所有类应该对于同一类性质的变化应该是共同封闭的。一个变化若对包产生影响，则将对包中所有的类产生影响。而对其它的包不造成影响。 
- CRP共同重用原则（Common Resue Principle）： 
　　一个包中的所有类应该是共同重用的。如果重用了包中的一个类，那么就要重用包中的所有类。 
- ADP无环依赖原则（Acyclic Dependencies Principle）： 
　　在包的依赖关系图中不允许存在环。 
- SDP稳定依赖原则（Stable Dependencies Principle）： 
　　易变化包不应该依赖稳定包。 
- SＡP稳定抽象原则（Stable Abstract Principle）： 
　　朝着抽象方向扩展。
