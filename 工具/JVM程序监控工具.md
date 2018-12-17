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

- 结果说明

```
显示内容说明如下（部分结果是通过其他其他参数显示的，暂不说明）：
         S0C：年轻代中第一个survivor（幸存区）的容量 (字节) 
         S1C：年轻代中第二个survivor（幸存区）的容量 (字节) 
         S0U：年轻代中第一个survivor（幸存区）目前已使用空间 (字节) 
         S1U：年轻代中第二个survivor（幸存区）目前已使用空间 (字节) 
         EC：年轻代中Eden（伊甸园）的容量 (字节) 
         EU：年轻代中Eden（伊甸园）目前已使用空间 (字节) 
         OC：Old代的容量 (字节) 
         OU：Old代目前已使用空间 (字节) 
         PC：Perm(持久代)的容量 (字节) 
         PU：Perm(持久代)目前已使用空间 (字节) 
         YGC：从应用程序启动到采样时年轻代中gc次数 
         YGCT：从应用程序启动到采样时年轻代中gc所用时间(s) 
         FGC：从应用程序启动到采样时old代(全gc)gc次数 
         FGCT：从应用程序启动到采样时old代(全gc)gc所用时间(s) 
         GCT：从应用程序启动到采样时gc用的总时间(s) 
         NGCMN：年轻代(young)中初始化(最小)的大小 (字节) 
         NGCMX：年轻代(young)的最大容量 (字节) 
         NGC：年轻代(young)中当前的容量 (字节) 
         OGCMN：old代中初始化(最小)的大小 (字节) 
         OGCMX：old代的最大容量 (字节) 
         OGC：old代当前新生成的容量 (字节) 
         PGCMN：perm代中初始化(最小)的大小 (字节) 
         PGCMX：perm代的最大容量 (字节)   
         PGC：perm代当前新生成的容量 (字节) 
         S0：年轻代中第一个survivor（幸存区）已使用的占当前容量百分比 
         S1：年轻代中第二个survivor（幸存区）已使用的占当前容量百分比 
         E：年轻代中Eden（伊甸园）已使用的占当前容量百分比 
         O：old代已使用的占当前容量百分比 
         P：perm代已使用的占当前容量百分比 
         S0CMX：年轻代中第一个survivor（幸存区）的最大容量 (字节) 
         S1CMX ：年轻代中第二个survivor（幸存区）的最大容量 (字节) 
         ECMX：年轻代中Eden（伊甸园）的最大容量 (字节) 
         DSS：当前需要survivor（幸存区）的容量 (字节)（Eden区已满） 
         TT： 持有次数限制 
         MTT ： 最大持有次数限制 
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