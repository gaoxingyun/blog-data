---
title: JVM基础知识
date: 2017-08-15 11:16:47
categories: 
- 基础
tags:
- 基础
- jvm
- java
---

## JVM运行时结构

### JVM运行时数据区

#### PC 寄存器

- Java 虚拟机可以支持多条线程同时执行(可参考《Java 语言规范》第 17 章)，每一条 Java 虚拟机线程都有自己的PC(Program Counter)寄存器。在任意时刻，一条Java虚拟机线程 只会执行一个方法的代码，这个正在被线程执行的方法称为该线程的当前方法(Current Method，§2.6)。如果这个方法不是 native 的，那 PC 寄存器就保存 Java 虚拟机正在执行的 字节码指令的地址，如果该方法是 native 的，那 PC 寄存器的值是 undefined。PC 寄存器的容 量至少应当能保存一个 returnAddress 类型的数据或者一个与平台相关的本地指针的值。

#### Java 虚拟机栈
- 每一条 Java 虚拟机线程都有自己私有的 Java 虚拟机栈(Java Virtual Machine Stack)，这个栈与线程同时创建，用于存储栈帧。

##### 栈帧
- 栈帧(Frame)是用来存储数据和部分过程结果的数据结构，同时也被用来处理动态链接 (Dynamic Linking)、方法返回值和异常分派(Dispatch Exception)。
- 栈帧随着方法调用而创建，随着方法结束而销毁——无论方法是正常完成还是异常完成(抛出 了在方法内未被捕获的异常)都算作方法结束。栈帧的存储空间分配在 Java 虚拟机栈(§2.5.5) 之中，每一个栈帧都有自己的局部变量表(Local Variables，§2.6.1)、操作数栈(Operand Stack，§2.6.2)和指向当前方法所属的类的运行时常量池(§2.5.5)的引用。
- 局部变量表和操作数栈的容量是在编译期确定，并通过方法的 Code 属性(§4.7.3)保存及 提供给栈帧使用。因此，栈帧容量的大小仅仅取决于 Java 虚拟机的实现和方法调用时可被分配的内存。
- 在一条线程之中，只有目前正在执行的那个方法的栈帧是活动的。这个栈帧就被称为是当前栈帧(Current Frame)，这个栈帧对应的方法就被称为是当前方法(Current Method)，定义这个方法的类就称作当前类(Current Class)。对局部变量表和操作数栈的各种操作，通常都指的是对当前栈帧的对局部变量表和操作数栈进行的操作。
- 如果当前方法调用了其他方法，或者当前方法执行结束，那这个方法的栈帧就不再是当前栈帧了。当一个新的方法被调用，一个新的栈帧也会随之而创建，并且随着程序控制权移交到新的方法而成为新的当前栈帧。当方法返回的之际，当前栈帧会传回此方法的执行结果给前一个栈帧，在方法返回之后，当前栈帧就随之被丢弃，前一个栈帧就重新成为当前栈帧了。 请读者特别注意，栈帧是线程本地私有的数据，不可能在一个栈帧之中引用另外一条线程的栈帧。

###### 局部变量表
- 每个栈帧(§2.6)内部都包含一组称为局部变量表(Local Variables)的变量列表。栈 帧中局部变量表的长度由编译期决定，并且存储于类和接口的二进制表示之中，既通过方法的 Code 属性(§4.7.3)保存及提供给栈帧使用。
- 一个局部变量可以保存一个类型为 boolean、byte、char、short、float、reference 和 returnAddress 的数据，两个局部变量可以保存一个类型为 long 和 double 的数据。
- 局部变量使用索引来进行定位访问，第一个局部变量的索引值为零，局部变量的索引值是从零 至小于局部变量表最大容量的所有整数。
- long 和 double 类型的数据占用两个连续的局部变量，这两种类型的数据值采用两个局部变 量之中较小的索引值来定位。例如我们讲一个 double 类型的值存储在索引值为 n 的局部变量中， 实际上的意思是索引值为 n 和 n+1 的两个局部变量都用来存储这个值。索引值为 n+1 的局部变量 是无法直接读取的，但是可能会被写入，不过如果进行了这种操作，就将会导致局部变量 n 的内 容失效掉。
- 上文中提及的局部变量 n 的 n 值并不要求一定是偶数，Java 虚拟机也不要求 double 和 long 类型数据采用 64 位对其的方式存放在连续的局部变量中。虚拟机实现者可以自由地选择适当的方 式，通过两个局部变量来存储一个 double 或 long 类型的值。
- Java 虚拟机使用局部变量表来完成方法调用时的参数传递，当一个方法被调用的时候，它的 参数将会传递至从 0 开始的连续的局部变量表位置上。特别地，当一个实例方法被调用的时候， 第 0 个局部变量一定是用来存储被调用的实例方法所在的对象的引用(即 Java 语言中的“this” 关键字)。后续的其他参数将会传递至从 1 开始的连续的局部变量表位置上。
###### 操作数栈
- 每一个栈帧(§2.6)内部都包含一个称为操作数栈(Operand Stack)的后进先出 (Last-In-First-Out，LIFO)栈。栈帧中操作数栈的长度由编译期决定，并且存储于类和接 口的二进制表示之中，既通过方法的 Code 属性(§4.7.3)保存及提供给栈帧使用。
- 在上下文明确，不会产生误解的前提下，我们经常把“当前栈帧的操作数栈”直接简称为“操 作数栈”。
- 操作数栈所属的栈帧在刚刚被创建的时候，操作数栈是空的。Java 虚拟机提供一些字节码指 令来从局部变量表或者对象实例的字段中复制常量或变量值到操作数栈中，也提供了一些指令用于 从操作数栈取走数据、操作数据和把操作结果重新入栈。在方法调用的时候，操作数栈也用来准备 调用方法的参数以及接收方法返回结果。举个例子，iadd 字节码指令的作用是将两个 int 类型的数值相加，它要求在执行的之前操作 数栈的栈顶已经存在两个由前面其他指令放入的 int 型数值。在 iadd 指令执行时，2 个 int 值 从操作栈中出栈，相加求和，然后将求和结果重新入栈。在操作数栈中，一项运算常由多个子运算 (Subcomputations)嵌套进行，一个子运算过程的结果可以被其他外围运算所使用。
- 每一个操作数栈的成员(Entry)可以保存一个 Java 虚拟机中定义的任意数据类型的值，包 括 long 和 double 类型。
- 在操作数栈中的数据必须被正确地操作，这里正确操作是指对操作数栈的操作必须与操作数栈 栈顶的数据类型相匹配，例如不可以入栈两个 int 类型的数据，然后当作 long 类型去操作他们， 或者入栈两个 float 类型的数据，然后使用 iadd 指令去对它们进行求和。有一小部分 Java 虚 拟机指令(例如 dup 和 swap 指令)可以不关注操作数的具体数据类型，把所有在运行时数据区 中的数据当作裸类型(Raw Type)数据来操作，这些指令不可以用来修改数据，也不可以拆散那 些原本不可拆分的数据，这些操作的正确性将会通过 Class 文件的校验过程(§4.10)来强制保 障。
- 在任意时刻，操作数栈都会有一个确定的栈深度，一个 long 或者 double 类型的数据会占用 两个单位的栈深度，其他数据类型则会占用一个单位深度。
###### 动态链接
- 每一个栈帧(§2.6)内部都包含一个指向运行时常量池(§2.5.5)的引用来支持当前方法 的代码实现动态链接(Dynamic Linking)。在 Class 文件里面，描述一个方法调用了其他方法， 或者访问其成员变量是通过符号引用(Symbolic Reference)来表示的，动态链接的作用就是 将这些符号引用所表示的方法转换为实际方法的直接引用。类加载的过程中将要解析掉尚未被解析 的符号引用，并且将变量访问转化为访问这些变量的存储结构所在的运行时内存位置的正确偏移 量。
- 由于动态链接的存在，通过晚期绑定(Late Binding)使用的其他类的方法和变量在发生 变化时，将不会对调用它们的方法构成影响。
###### 方法正常调用完成
###### 方法异常调用完成

#### Java 堆
- 在 Java 虚拟机中，堆(Heap)是可供各条线程共享的运行时内存区域，也是供所有类实例 和数组对象分配内存的区域。
- Java 堆在虚拟机启动的时候就被创建，它存储了被自动内存管理系统(Automatic Storage Management System，也即是常说的“Garbage Collector(垃圾收集器)”)所管理的各种 对象，这些受管理的对象无需，也无法显式地被销毁。本规范中所描述的 Java 虚拟机并未假设采 用什么具体的技术去实现自动内存管理系统。虚拟机实现者可以根据系统的实际需要来选择自动内 存管理技术。Java 堆的容量可以是固定大小的，也可以随着程序执行的需求动态扩展，并在不需 要过多空间时自动收缩。Java 堆所使用的内存不需要保证是连续的。

#### 方法区
- 在Java虚拟机中，方法区(Method Area)是可供各条线程共享的运行时内存区域。方法 区与传统语言中的编译代码储存区(Storage Area Of Compiled Code)或者操作系统进程 的正文段(Text Segment)的作用非常类似，它存储了每一个类的结构信息，例如运行时常量池(Runtime Constant Pool)、字段和方法数据、构造函数和普通方法的字节码内容、还包括一些在类、实例、接口初始化时用到的特殊方法(§2.9)。
- 方法区在虚拟机启动的时候被创建，虽然方法区是堆的逻辑组成部分，但是简单的虚拟机实现 可以选择在这个区域不实现垃圾收集。这个版本的 Java 虚拟机规范也不限定实现方法区的内存位置和编译代码的管理策略。方法区的容量可以是固定大小的，也可以随着程序执行的需求动态扩展，并在不需要过多空间时自动收缩。方法区在实际内存空间中可以是不连续的。

#### 运行时常量池
- 运行时常量池(Runtime Constant Pool)是每一个类或接口的常量池(Constant_Pool， §4.4)的运行时表示形式，它包括了若干种不同的常量:从编译期可知的数值字面量到必须运行 期解析后才能获得的方法或字段引用。运行时常量池扮演了类似传统语言中符号表(Symbol Table)的角色，不过它存储数据范围比通常意义上的符号表要更为广泛。
- 每一个运行时常量池都分配在 Java 虚拟机的方法区之中(§2.5.4)，在类和接口被加载到虚拟机后，对应的运行时常量池就被创建出来。

#### 本地方法栈
- Java虚拟机实现可能会使用到传统的栈(通常称之为“C Stacks”)来支持native方法 (指使用 Java 以外的其他语言编写的方法)的执行，这个栈就是本地方法栈(Native Method Stack)。当 Java 虚拟机使用其他语言(例如 C 语言)来实现指令集解释器时，也会使用到本地 方法栈。如果 Java 虚拟机不支持 natvie 方法，并且自己也不依赖传统栈的话，可以无需支持本 地方法栈，如果支持本地方法栈，那这个栈一般会在线程创建的时候按线程分配。
- Java 虚拟机规范允许本地方法栈被实现成固定大小的或者是根据计算动态扩展和收缩的。如 果采用固定大小的本地方法栈，那每一条线程的本地方法栈容量应当在栈创建的时候独立地选定。 一般情况下，Java 虚拟机实现应当提供给程序员或者最终用户调节虚拟机栈初始容量的手段，对 于长度可动态变化的本地方法栈来说，则应当提供调节其最大、最小容量的手段。


### 线程间数据共享
- 首先，JVM将内存组织为主内存和工作内存两个部分。主内存主要包括本地方法区和堆。每个线程都有一个工作内存，工作内存中主要包括两个部分，一个是属于该线程私有的栈和对主存部分变量拷贝的寄存器(包括程序计数器PC和cup工作的高速缓存区)。  
 1. 所有的变量都存储在主内存中(虚拟机内存的一部分)，对于所有线程都是共享的。
 2. 每条线程都有自己的工作内存，工作内存中保存的是主存中某些变量的拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量。
 3. 线程之间无法直接访问对方的工作内存中的变量，线程间变量的传递均需要通过主内存来完成。


#### 平台内存模型

- 大多平台都有主内存（内存 线（进）程共享）和处理器高速缓存（处理器缓存 线（进）程相关）的概念，Java为了平台无关性定义的自己的内存模型。

#### 内存操作指令

- JVM规范定义了线程对内存间交互操作：
 1. Lock(锁定)：作用于主内存中的变量，把一个变量标识为一条线程独占的状态。
 2. Read(读取)：作用于主内存中的变量，把一个变量的值从主内存传输到线程的工作内存中。
 3. Load(加载)：作用于工作内存中的变量，把read操作从主内存中得到的变量的值放入工作内存的变量副本中。
 4. Use(使用)：作用于工作内存中的变量，把工作内存中一个变量的值传递给执行引擎。
 5. Assign(赋值)：作用于工作内存中的变量，把一个从执行引擎接收到的值赋值给工作内存中的变量。
 6. Store(存储)：作用于工作内存中的变量，把工作内存中的一个变量的值传送到主内存中。
 7. Write(写入)：作用于主内存中的变量，把store操作从工作内存中得到的变量的值放入主内存的变量中。
 8. Unlock(解锁)：作用于主内存中的变量，把一个处于锁定状态的变量释放出来，之后可被其它线程锁定。



## JVM优化

### JVM编译期优化

#### 性能优化

##### 常量优化

###### 常量传播
- 在编译优化时，将能够计算出结果的变量直接替换为常量。

###### 常量折叠
- 在编译优化时，多个变量进行计算时，而且能够直接计算出结果，那么变量将由常量直接替换。

#### 解语法糖

##### 类型擦除
- 在编译时，对范型进行类型擦除。

##### 自动装箱、拆箱
- 对基本类型和包装类型进行装箱，拆箱。


### JVM运行期优化


