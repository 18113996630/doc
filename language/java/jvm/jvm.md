# JVM
[Java虚拟机案例](https://github.com/JeffLi1993/jvm-core-learning-example)

[java g1笔记](https://mp.weixin.qq.com/s/vuzXjtB2-g2hRXauN0H36Q)

[必知必会的5道JVM面试题](https://mp.weixin.qq.com/s/l8VH8bRHamiZNgBWNId3_w)
              
### jvm调优工具

一、JVM调优工具



> Jconsole，jProfile，VisualVM。



Jconsole : jdk自带，功能简单，但是可以在系统有一定负荷的情况下使用。对垃圾回收算法有很详细的跟踪。

 

JProfiler：商业软件，需要付费。功能强大。

 

VisualVM：JDK自带，功能强大，与JProfiler类似。推荐。

 

二、如何调优？


观察内存释放情况、集合类检查、对象树。



上面这些调优工具都提供了强大的功能，但是总的来说一般分为以下几类功能。

 

堆信息查看

![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/01.png)





可查看堆空间大小分配（年轻代、年老代、持久代分配）

提供即时的垃圾回收功能

垃圾监控（长时间监控回收情况）



![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/02.png)

 

查看堆内类、对象信息查看：数量、类型等


![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/03.png)




对象引用情况查看



有了堆信息查看方面的功能，我们一般可以顺利解决以下问题：

  --年老代年轻代大小划分是否合理

  --内存泄漏

  --垃圾回收算法设置是否合理

 

三、线程监控

![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/04.png)


线程信息监控：系统线程数量。

线程状态监控：各个线程都处在什么样的状态下





![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/05.png)

Dump线程详细信息：查看线程内部运行情况

死锁检查



1、热点分析



![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/06.png)

 

CPU热点：检查系统哪些方法占用的大量CPU时间



内存热点：检查哪些对象在系统中数量最大（一定时间内存活对象和销毁对象一起统计）

 

这两个东西对于系统优化很有帮助。我们可以根据找到的热点，有针对性的进行系统的瓶颈查找和进行系统优化，而不是漫无目的的进行所有代码的优化。



2、快照



快照是系统运行到某一时刻的一个定格。在我们进行调优的时候，不可能用眼睛去跟踪所有系统变化，依赖快照功能，我们就可以进行系统两个不同运行时刻，对象（或类、线程等）的不同，以便快速找到问题。



举例说，我要检查系统进行垃圾回收以后，是否还有该收回的对象被遗漏下来的了。那么，我可以在进行垃圾回收前后，分别进行一次堆情况的快照，然后对比两次快照的对象情况。

 

四、内存泄漏检查


内存泄漏是比较常见的问题，而且解决方法也比较通用，这里可以重点说一下，而线程、热点方面的问题则是具体问题具体分析了。



内存泄漏一般可以理解为系统资源（各方面的资源，堆、栈、线程等）在错误使用的情况下，导致使用完毕的资源无法回收（或没有回收），从而导致新的资源分配请求无法完成，引起系统错误。



内存泄漏对系统危害比较大，因为他可以直接导致系统的崩溃。



需要区别一下，内存泄漏和系统超负荷两者是有区别的，虽然可能导致的最终结果是一样的。内存泄漏是用完的资源没有回收引起错误，而系统超负荷则是系统确实没有那么多资源可以分配了（其他的资源都在使用）。

 

 

1、年老代堆空间被占满



异常： java.lang.OutOfMemoryError: Java heap space



说明：这是最典型的内存泄漏方式，简单说就是所有堆空间都被无法回收的垃圾对象占满，虚拟机无法再在分配新空间。


![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/07.png)




如上图所示，这是非常典型的内存泄漏的垃圾回收情况图。所有峰值部分都是一次垃圾回收点，所有谷底部分表示是一次垃圾回收后剩余的内存。连接所有谷底的点，可以发现一条由底到高的线，这说明，随时间的推移，系统的堆空间被不断占满，最终会占满整个堆空间。因此可以初步认为系统内部可能有内存泄漏。（上面的图仅供示例，在实际情况下收集数据的时间需要更长，比如几个小时或者几天）

 

解决方法：这种方式解决起来也比较容易，一般就是根据垃圾回收前后情况对比，同时根据对象引用情况（常见的集合对象引用）分析，基本都可以找到泄漏点。

 

2、持久代被占满



异常：java.lang.OutOfMemoryError: PermGen space



说明：Perm空间被占满。无法为新的class分配存储空间而引发的异常。这个异常以前是没有的，但是在Java反射大量使用的今天这个异常比较常见了。主要原因就是大量动态反射生成的类不断被加载，最终导致Perm区被占满。



更可怕的是，不同的classLoader即便使用了相同的类，但是都会对其进行加载，相当于同一个东西，如果有N个classLoader那么他将会被加载N次。因此，某些情况下，这个问题基本视为无解。当然，存在大量classLoader和大量反射类的情况其实也不多。



解决方法



-XX:MaxPermSize=16m

换用JDK。比如JRocket。

 

3、堆栈溢出



异常：java.lang.StackOverflowError



说明：这个就不多说了，一般就是递归没返回，或者循环调用造成

 

4、线程堆栈满



异常：Fatal: Stack size too small

说明：java中一个线程的空间大小是有限制的。JDK5.0以后这个值是1M。与这个线程相关的数据将会保存在其中。但是当线程空间满了以后，将会出现上面异常。



解决方法：增加线程栈大小。-Xss2m。但这个配置无法解决根本问题，还要看代码部分是否有造成泄漏的部分。

 

5、系统内存被占满



异常：java.lang.OutOfMemoryError: unable to create new native thread

说明：这个异常是由于操作系统没有足够的资源来产生这个线程造成的。系统创建线程时，除了要在Java堆中分配内存外，操作系统本身也需要分配资源来创建线程。因此，当线程数量大到一定程度以后，堆中或许还有空间，但是操作系统分配不出资源来了，就出现这个异常了。



分配给Java虚拟机的内存愈多，系统剩余的资源就越少，因此，当系统内存固定时，分配给Java虚拟机的内存越多，那么，系统总共能够产生的线程也就越少，两者成反比的关系。同时，可以通过修改-Xss来减少分配给单个线程的空间，也可以增加系统总共内生产的线程数。



解决：

    1. 重新设计系统减少线程数量。

    2. 线程数量不能减少的情况下，通过-Xss减小单个线程大小。以便能生产更多的线程。
    
    
    
    
    
    
    
    
    
    
    
    
    
    
------------------------------------------------
JVM学习

class内加载机制
JVM内存划分,运行时数据区
回收算法
调优

## jre、jdk、jvm的区别

## 运行时数据区

程序计数器、Java 虚拟机栈、本地方法栈、Java 堆、方法区、运行时常量池、直接内存

![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/08.png?raw=true)

### 堆(heap)
线程共享
最大内存区域,存储各类生成的对象、数组、常量池(jdk1.8)、静态变量(jdk1.8)
Java堆是垃圾收集器管理的主要区域。Java堆分为新生代和老年代

### 程序计数器(Program Counter Register)
线程私有

如果线程正在执行的是一个 Java 方法，计数器记录的是正在执行的虚拟机字节码指令的地址

如果正在执行的是 Native 方法，计数器的值为空(native method:java调用非java)

为什么要有?
线程切换后能恢复到正确的执行位置，每条线程都需要一个独立的程序计数器，各线程之间的计数器互不影响，独立存储

### 虚拟机栈(VM Stack)
线程私有,生命周期与线程相同

Java方法执行的内存模型：每个方法被执行的时候都会同时创建一个栈帧（Stack Frame）用于存储局部变量表、操作栈、动态链接、方法出口等信息。

每一个方法被调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程

局部变量表存放8种基本数据类型、对象引用、returnAddress

### 本地方法栈(Native Method Stack)

和本地方法栈类似,虚拟机栈为执行本地方法服务,本地方法栈为Native方法服务

### 方法区(Method->MetaSpace)

jdk1.8废除永久代-->元空间（MetaSpace）

线程共享

存储类的元数据的，如虚拟机加载的类信息、编译后的代码等

![image](https://github.com/leelovejava/doc/blob/master/img/java/jvm/09.png?raw=true)




## 改进
jdk1.8

废除永生代,改为Metaspace（元数据区）

常量池和静态变量放到Java 堆

将类元数据放到本地内存中

## 监控
VisualVM
	集成命令行JDK工具和轻量级分析功能的可视化工具
	jvisualvm.exe
	Debug with VisualVM
	[性能分析神器VisualVM](http://www.cnblogs.com/wade-xu/p/4369094.html)


## JVM性能调优
java内存模型总体概述、类加载过程和classloader、运行时数据区当中的总体内容、编译原理

内存区域与内存溢出异常、虚拟机对象、程序计数器、java栈、本地方法栈、操作数、方法区、堆内存和元数据等等

Classloader的知识详细、默认全盘负责机制、从JDK源码来理解双亲委派模式、如何打破双亲委派？为什么需要打破？

虚拟机性能监控与故障处理、jvm基本命令，jinfo命令的使用jmap命令使用、jstak命令的使用、使用jvisualvm分析

垃圾收集器与内存分配策略、垃圾回收算法与基础、串型收集器、并行收集器、内存分配与回收策略。

程序编译与代码优化、运行期优化、编译期优化、JVM调优的本质是什么？什么是轻gc？什么是Full gc？如何调优

JVM执行子系统、类文件结构、类加载机制、字节码执行引擎、字节码编译模式、如何改变字节码编译模式？


面试题?
01).基本概念

JVM是包括一套字节码指令集、一组寄存器、一个栈、堆、一个垃圾回收，和一个存储方法域。

JVM是运行在操作系统之上的，它与硬件没有直接的交互。

02).谈谈你对解析与分派的认识。

1.方法在程序真正运行之前就有一个可确定的调用版本，并且这个方法的调用版本在运行期间是不可变的，即“编译时可知，运行不可以变”，这类目标的方法的调用称之为解析

2.解析调用一定是个静态的过程，在编译期就完全确定，在类加载的解析阶段就将涉及的符号引用全部转变为可以确定的直接引用，不会延迟到运行期再去完成。

分派（Dispatch）调用则可能是静态的也可能是动的。于是分派方式就有静态分派和动态分派。

静态分派的最直接的解释是在重载的时候是通过参数的静态类型而不是实际类型作为判断依据的。因此在编译阶段，Javac编译器会根据参数的静态类型决定使用哪个重载版本。

显然这里不可能根据静态类型来决定调用那个方法。导致这个现象很明显的原因是因为这两个变量的实际类型不一样，jvm根据实际类型来分派方法执行版本。


03).你知道哪些或者你们线上使⽤什么GC策略？它有什么优势，适⽤于什么场景？

参考 触发JVM进行Full GC的情况及应对策略。

https://blog.csdn.net/chenleixing/article/details/46706039/

04).Java类加载器包括⼏种？它们之间的⽗⼦关系是怎么样的？双亲委派机制是什么意思？有什么好处？

如何⾃定义⼀个类加载器？你使⽤过哪些或者你在什么场景下需要⼀个⾃定义的类加载器吗？

分类:

Bootstrap ClassLoader(系统目录 启动类加载)、

ExtClassLoader(扩展目录 扩展类加载器)、

AppClassLoader(应用程序加载)

父子关系(组合关系,不是继承)如下:
自定义类加载->应用程序加载->扩展类加载(ext父为null) ->启动类加载(顶级)

双亲委派机制：类加载器收到类加载请求，自己不加载，向上委托给父类加载，父类加载不了，再自己加载

双亲委派的好处:

A.防止重复加载

比如你有一个类，在不采用双亲委派模型的情况下，可能会有不同的加载器去加载这个类，不同类加载器加载出来的 Class 文件必然不相同，这样就造成了不一致性

B.保证类来源的安全性
[深入理解Java类加载器(ClassLoader)](https://blog.csdn.net/javazejian/article/details/73413292/)

工作过程：

如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载器都是如此，
因此所有的加载请求都应该传送到顶层的启动类加载器，只有当父加载器无法完成这个加载请求时，子加载器才会尝试自己去加载。

自定义类加载过程:

A. 继承ClassLoader    

B. 重写findClass()方法  
 
C. 调用defineClass（）方法

自定义类加载的意义

A. 加载特定路径的class文件

B. 加载一个加密的网络class文件.网络的输入流中读取类(不从类文件),涉及加密和解密操作

C. 热部署加载class文件.定义类的实现机制，实现类的热部署


05).堆内存设置的参数是什么？
-Xmx 设置堆的最大空间大小

-Xms 设置堆的最小空间大小

06).Perm Space中保存什么数据？会引起OutOfMemory吗？
加载class文件

会引起，出现异常可以设置 -XX:PermSize 的大小。JDK 1.8后，字符串常量不存放在永久带，而是在堆内存中，JDK8以后没有永久代概念，而是用元空间替代，元空间不存在虚拟机中，二是使用本地内存。

详细查看Java8内存模型—永久代(PermGen)和元空间(Metaspace)

https://www.cnblogs.com/paddix/p/5309550.html/

07).做GC时，⼀个对象在内存各个Space中被移动的顺序是什么？

标记清除法，复制算法，标记整理、分代算法。

新生代一般采用复制算法 GC，老年代使用标记整理算法。

垃圾收集器：串行新生代收集器、串行老生代收集器、并行新生代收集器、并行老年代收集器。

CMS（Current Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器，它是一种并发收集器，采用的是Mark-Sweep算法。

详见 Java GC机制。

http://www.cnblogs.com/dolphin0520/p/3783345.htmll/

08).你有没有遇到过OutOfMemory问题？你是怎么来处理这个问题的？处理 过程中有哪些收获？
permgen space、heap space 错误。

常见的原因

内存加载的数据量太大：一次性从数据库取太多数据；

集合类中有对对象的引用，使用后未清空，GC不能进行回收；

代码中存在循环产生过多的重复对象；

启动参数堆内存值小。

详见 Java 内存溢出（java.lang.OutOfMemoryError）的常见情况和处理方式总结。

http://outofmemory.cn/c/java-outOfMemoryError/

09).StackOverflow异常有没有遇到过？⼀般你猜测会在什么情况下被触发？如何指定⼀个线程的堆栈⼤⼩？⼀般你们写多少？

栈内存溢出，一般由栈内存的局部变量过爆了，导致内存溢出。出现在递归方法，参数个数过多，递归过深，递归没有出口。

10).内存模型以及分区，需要详细到每个区放什么?

方法区:存储类的信息、常量、静态变量、方法名、访问权限、返回值

堆:存储对象实例(所有new操作)

程序计数器:下一条需要执行的字节码指令

虚拟机栈:基本数据变量、对象引用变量、局部变量,（栈内存）,为虚拟机执行java方法服务：方法被调用时创建栈帧-->局部变量表->局部变量、对象引用

本地方法栈:为本地方法服务

堆和方法区是所有线程共有的，而虚拟机栈、本地方法栈和程序计数器是线程私有的

JVM结构
类加载器、运行时数据区(方法区、Java堆、本地方法栈、堆、程序计数器)、执行引擎、本地接口、本地库

JVM中堆 栈 静态区的作用?[深圳阿尔法2017]
堆区:heap

1.存储的全部是对象(new)，每个对象都包含一个与之对应的class的信息。(class的目的是得到操作指令)

2.堆区(heap)线程共享，堆中不存放基本类型和对象引用，只存放对象本身

3.一般由程序员分配释放， 若程序员不释放，程序结束时可能由OS回收

栈区:stack
1.每个线程包含一个栈区，栈中只保存基础数据类型的对象和自定义对象的引用(不是对象)，对象都存放在堆区中

2.每个栈中的数据(原始类型和对象引用)都是私有的，其他栈不能访问。

3.栈分为3个部分：基本类型变量区、执行环境上下文、操作指令区(存放操作指令)。

4.由编译器自动分配释放 ，存放函数的参数值，局部变量的值等

静态区/方法区:method
1.方法区又叫静态区，跟堆一样，被所有的线程共享。方法区包含所有的class和static变量。

2.方法区中包含的都是在整个程序中永远唯一的元素，如class，static变量。

3.全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域

11).分派：静态分派与动态分派。

12).虚拟机在运行时有哪些优化策略

[JVM(1)---虚拟机在运行期的优化策略](https://www.cnblogs.com/kubidemanong/p/9457004.html)
公共子表达式消除
数组范围检查消除
方法内联
逃逸分析

13).请解释`StackOverflowError`和`OutOfMemeryError`的区别？
`StackOverflowError`:线程栈的溢出
`OutOfMemoryError`:堆内存不够(程序死循环)

14).在JVM中，如何判断一个对象是否死亡？
可达性分析算法

“GC Roots”的对象为起始点，从这些节点向下搜索，搜索所走过的路称为引用链，当一个对象没有任何引用链相连时，则证明该对象是不可引用的

GC Root对象
1.虚拟机栈中引用的对象。 
2.方法区中类静态属性引用的对象。 
3.方法区常量引用的对象。 
4.本地方法栈中JNI引用的对象。

[jvm 判断对象是否已经死亡](https://blog.csdn.net/Next__One/article/details/78755731)

强引用（Strong Reference）、软引用（Soft Reference）、弱引用（Weak Reference）、虚引用（Phantom Reference）

15).JDK 1.8之后Perm Space有哪些变动? MetaSpace⼤⼩默认是⽆限的么? 还是你们会通过什么⽅式来指定⼤⼩?
JDK 1.8后用元空间替代了 Perm Space；字符串常量存放到堆内存中。

MetaSpace大小默认没有限制，一般根据系统内存的大小。JVM会动态改变此值。

-XX:MetaspaceSize：分配给类元数据空间（以字节计）的初始大小（Oracle逻辑存储上的初始高水位，the initial high-water-mark）。此值为估计值，MetaspaceSize的值设置的过大会延长垃圾回收时间。垃圾回收过后，引起下一次垃圾回收的类元数据空间的大小可能会变大。

-XX:MaxMetaspaceSize：分配给类元数据空间的最大值，超过此值就会触发Full GC，此值默认没有限制，但应取决于系统内存的大小。JVM会动态地改变此值

16).jstack 是⼲什么的? jstat 呢？如果线上程序周期性地出现卡顿，你怀疑可能是GC导致的，你会怎么来排查这个问题？线程⽇志⼀般你会看其中的什么部分？
jstack ：Java 堆栈跟踪工具(生成 Java 虚拟机当前时刻的线程快照)

jstat ：虚拟机统计信息监控工具

jps ：虚拟机进程状况工具(显示指定系统内所有的HotSpot虚拟机进程)

jvisualvm : 监控内存泄露，跟踪垃圾回收、执行时内存、cpu分析、线程分析。

详见Java jvisualvm简要说明，可参考 线上FullGC频繁的排查。

[Java jvisualvm简要说明](https://blog.csdn.net/a19881029/article/details/8432368/)

[线上FullGC频繁的排查](https://blog.csdn.net/wilsonpeng3/article/details/70064336/)

17).如果对象的引用被置为null，垃圾收集器是否会立即释放对象占用的内存？
不会，在下一个垃圾回收周期中，这个对象将是可被回收的。

也就是说当一个对象的引用变为 null 时，并不会被垃圾收集器立刻回收，而是在下一次垃圾回收时才会释放其占用的内存


18).JVM加载class文件的原理机制
类装载到jvm中运行,实质是把类文件从硬盘读取到内存中

19).JVM执行程序的过程
A.加载.class文件

B.管理并分配内存

C.执行垃圾回收

JVM运行过程

① Java源文件—->编译器—->字节码文件

② 字节码文件—->JVM—->机器码

21).JVM调优

A.堆大小设置

B.回收器选择

1、吞吐量优先的并行收集器

2、响应时间优先的并发收集器
 
C.jvm常用启动参数

堆大小设置（堆 = 年轻代 + 年老代+ 持久代）
垃圾回收器设置（串行收集器、并行收集器、并发收集器 ）
辅助信息参数设置

---------------
[讲讲jvm运行时数据区](https://mp.weixin.qq.com/s/li3ISdodGu2EK_Fo_4NJPA)
[什么情况下会发生栈内存溢出](https://mp.weixin.qq.com/s/li3ISdodGu2EK_Fo_4NJPA)
[JVM的内存结构，Eden和Survivor比例](https://blog.csdn.net/bluetjs/article/details/52874852)
[JVM内存为什么要分成新生代，老年代，持久代。新生代中为什么要分为Eden和Survivor](https://blog.csdn.net/bluetjs/article/details/52874852)
[JVM中一次完整的GC流程是怎样的，对象如何晋升到老年代，说说你知道的几种主要的JVM参数](https://www.jianshu.com/p/314272e6d35b)
[讲下cms和G1，包括原理，流程，优缺点](https://blog.csdn.net/zhanggang807/article/details/45956325)
[垃圾回收算法的实现原理](https://blog.csdn.net/zhanggang807/article/details/45956325)
[G1，包括原理，流程，优缺点](http://www.cnblogs.com/xiaoxi/p/6486852.html)
[CMS收集器学习笔记](https://mp.weixin.qq.com/s/OzE7WrvcGPEcf_UHj2a-lg)
谈谈你的GC调优思路?
[垃圾回收算法的实现原理](http://www.cnblogs.com/aspirant/p/8662690.html)
[当出现了内存溢出，你怎么排错](https://blog.csdn.net/wtt945482445/article/details/52483944)
[JVM内存模型的相关知识了解多少，比如重排序，内存屏障，happen-before，主内存，工作内存等](http://blog.csdn.net/javazejian/article/details/73413292)
[简单说说你了解的类加载器，可以打破双亲委派么，怎么打破](http://blog.csdn.net/javazejian/article/details/73413292)
[加载时机与加载过程](https://blog.csdn.net/justloveyou_/article/details/72466105)
[Java类加载的方式](http://blog.csdn.net/justloveyou_/article/details/72466416)
[Java对象的创建过程](http://blog.csdn.net/justloveyou_/article/details/72466416)
[你们线上应用的JVM参数有哪些](https://blog.csdn.net/ZYC88888/article/details/80353357)
[g1和cms区别,吞吐量优先和响应优先的垃圾收集器选择](https://blog.csdn.net/ZYC88888/article/details/80353357)
[怎么打出线程栈信息](http://www.cnblogs.com/kongzhongqijing/articles/3630264.html)
