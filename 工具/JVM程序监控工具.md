---
title: JVM程序监控工具
date: 2017-07-14 13:11:19
categories: 
- 工具
tags: 
- 工具
- java
- 监控
---


## JDK自带工具

#### 命令行

- [jps、jinfo、jstat、jstack、jmap、jconsole等命令简介](https://blog.csdn.net/caihaijiang/article/details/6084325)
- [JVM性能调优监控工具jps、jstack、jmap、jhat、jstat、hprof使用详解 - 实例分析](https://blog.csdn.net/DragonAssassin/article/details/51010947)

##### jstat
- [Java JVM- jstat查看jvm的GC情况[转]](https://www.cnblogs.com/aspirant/p/7001082.html)
```
# 查看gc情况
Usage: jstat --help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
  -? -h --help  Prints this help message.
  -help         Prints this help message.


jstat -options
-class
-compiler
-gc
-gccapacity
-gccause
-gcmetacapacity
-gcnew
-gcnewcapacity
-gcold
-gcoldcapacity
-gcutil
-printcompilation
```

##### jmap
-[java命令--jmap命令使用](https://www.cnblogs.com/kongzhongqijing/articles/3621163.html)
```
jmap
Usage:
    jmap -clstats <pid>
        to connect to running process and print class loader statistics
    jmap -finalizerinfo <pid>
        to connect to running process and print information on objects awaiting finalization
    jmap -histo[:live] <pid>
        to connect to running process and print histogram of java object heap
        if the "live" suboption is specified, only count live objects
    jmap -dump:<dump-options> <pid>
        to connect to running process and dump java heap
    jmap -? -h --help
        to print this help message

    dump-options:
      live         dump only live objects; if not specified,
                   all objects in the heap are dumped.
      format=b     binary format
      file=<file>  dump heap to <file>

    Example: jmap -dump:live,format=b,file=heap.bin <pid>
```

##### jstack
- [jmap和jstack使用](https://blog.csdn.net/sinat_29581293/article/details/70214436)

#### jconsole工具

#### jvisualvm工具

## 

#### JProfiler工具

## 性能分析工具

#### 阿里TProfiler工具
https://github.com/alibaba/TProfiler


## 开源监控系统

- zabbix
- Nagios