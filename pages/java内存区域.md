- 由JVM虚拟机规范制定
- 内存区域图
  JDK 1.8 之前:
  ![jdk8之前内存区域图.png](../assets/image_1654772017696_0.png)
  JDK8之后:
  方法区移除，用元空间替代
  ![JDK8内存区域.png](../assets/image_1654772089454_0.png)
- java内存区域划分
	- 程序计数器
	  定义:标识线程执行的字节码指令位置,读取程序计数器获取下一条执行指令位置
	  --->类似于cpu的寄存器计数器
	  作用:
	  1. 进行程序流程控制：顺序执行，分支，循环，跳转（break,continue,case标签等），方法，异常等都需要依赖它
	  2. 线程上下文切换需要标识线程当前执行指令位置
	- 虚拟机栈
	  定义：java方法执行的内存模型
	  运行：java方法开始执行，创建栈帧，入栈，方法执行完毕，出栈，无论方法正常完成还是异常完成都算作方法结束。
	  内存结构:
	  栈帧:
	  局部变量表:
	  局部变量表占用内存空间在编译期就能确认大小，运行期间不会变更(而对象实例大小只能在运行期才能确认)
	  局部变量槽:数据类型大小=数据类型占用数量*每个局部变量槽大小（64位虚拟机一个局部变量槽64字节）
	  动态链接:动态链接的作用就是为了将符号引用转换为调用方法的直接引用。
	  方法出口:
	  操作数栈:
	  ![java虚拟机栈结构图.png](../assets/java虚拟机栈结构图_1654775815697_0.png) 
	  内存错误异常:
	  OutOfMemoryError:
	  1. 线程申请内存空间不足时
	  2. 当栈支持动态扩展情况下（其他虚拟机实现,hotspot虚拟机实现不支持），扩展栈空间内存空间不足时
	  StackOverFlowError:
	  当栈不支持动态扩展（hopspot虚拟机）线程栈空间无法容纳新栈帧时候
	  而不是之前理解的：线程超过栈深度上限就抛出该错误异常，因为hotspot虚拟机没有直接设置栈深度的参数，而是通过设置栈空间大小(Stock Size) -Xss128k间接控制栈深度
	- 本地方法栈
	  定义:native本地方法执行的内存模型
	  基本和虚拟机栈差不多
	- java堆(也叫GC堆 Garbage Collected Heap)
	  id:: 62a1d414-b7ba-4c85-99c3-8a0f77190bc1
	  定义:java所有对象实例和数组对象绝大部分都在堆上分配
	  这句话不绝对了，是因为一系列编译优化技术（逃逸分析技术等）的出现，例如对象栈上分配，标量替换等。
	  内存结构:
	  1. 普通堆:
	  ![i堆内存结构.png](../assets/image_1654777019118_0.png) 
	  2. 线程私有分配缓冲区 TLAB（Thread Local Allocation Buffer）
	  定义:java堆内存为每个线程分配一块线程专用内存区域
	  提高多线程下内存分配效率--->减少并发 分配锁竞争
	  内存错误异常:
	  OutOfMemoryError:当对象实例内存空间不够分配，且堆无法扩展时
	  JVM参数: ((6298ce15-1863-4aae-99c9-5d8d7ae4dfdb))
	- 方法区
	  内存结构
	  方法区和永久代和元空间的关系--->类似接口与实现类的关系
	  ![方法区和永久代和元空间的关系.png](../assets/image_1654857588401_0.png)
	  永久代: JDK 1.8 之前的方法区实现，
	  元空间:JDK 1.8 及以后方法区实现,直接在本地内存中实现元空间
	  与永久代很大的不同就是，如果不指定大小的话，随着更多类的创建，虚拟机会耗尽所有可用的系统内存。
	  为什么要把永久代替换成元空间
	  hotspot永久代实现（已废弃，自己都承认永久代是一个不合理的设计），其他虚拟机例如BEA jrocket，IBM J9没有永久代概念
	  1.许多框架，机制存在动态类加载,可能会导致OutOfMemoryError内存异常（有上限 -XX：MaxPermSize）
	  2.极少数方法（例如String.intern）因为会因为永久代的原因导致不同虚拟机下有不同的表现
	  
	  运行时常量池（Runtime Constant Pool）
	  存储数据类型:
	  1. Class类文件结构中的常量池表
	  存放编译期生成的各种字面量(Literal)与符号引用(Symbolic Referenc),存入时机：类加载后存入运行时常量池
	  2. 运行时也可以将常量存放入运行时常量池
	  最典型场景：String.intern方法
	  
	  JVM参数
	  -XX：MaxMetaspaceSize 标志设置最大元空间大小，默认值为 unlimited，这意味着它只受系统内存的限制。
	  -XX：MetaspaceSize 调整标志定义元空间的初始大小如果未指定此标志，则 Metaspace 将根据运行时的应用程序需求动态地重新调整大小。
	  jdk8之前的永久代设置大小
	  -XX:PermSize=N //方法区 (永久代) 初始大小
	  -XX:MaxPermSize=N //方法区 (永久代) 最大大小
	- 字符串常量池
	  目的:JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。
	  hotspot虚拟机实现
	  HotSpot 虚拟机中字符串常量池的实现是 src/hotspot/share/classfile/stringTable.cpp ,
	  StringTable 本质上就是一个HashSet<String> 
	  StringTable 中保存的是字符串对象的引用，字符串对象的引用指向堆中的字符串对象。
	  JVM参数
	  -XX:StringTableSize 设置字符串常量池大小
	  
	  JDK1.7 之前，字符串常量池存放在永久代。
	  JDK1.7 字符串常量池和静态变量从永久代移动了 Java 堆中。
	-
	- 直接内存
	  定义:不是java虚拟机内存区域部分，《《java虚拟机规范》》之外的内存区域
	  JDK1.4 引入NIO,
	  它可以直接使用 Native 函数库直接分配堆外内存，然后通过一个存储在 Java 堆中的 DirectByteBuffer 对象作为这块内存的引用进行操作。
	  不受java内存堆大小限制，但受制于机器物理内存大小
	- java内存区域比较汇总表
	  
	  |内存区域|生命周期|内存错误异常|内存大小|存储数据类型|内存分配大小|内存回收|
	  |---|---|---|---|---|---|---|
	  |程序计数器|线程私有|虚拟机规范唯一没有规定抛出内存异常|一般很小||确定性|确定性,与线程生死相随,方法/线程结束，内存自然回收|
	  |虚拟机栈|线程私有|OutOfMemoryError,StackOverFlowError| -Xss128k指定栈空间大小|局部变量,方法出口|编译器可知|确定性,与线程生死相随,方法/线程结束，内存自然回收|
	  |本地方法栈|线程私有|OutOfMemoryError,StackOverFlowError||局部变量,方法出口|编译器可知|确定性,与线程生死相随,方法/线程结束，内存自然回收|
	  |堆|线程共有|OutOfMemoryError|-Xmx最大值,-Xms 最小值|对象实例|动态分配|垃圾回收器|
	  |方法区(逻辑概念上)|线程共享|OutOfMemoryError|-XX:MaxMetaspaceSize最大值,-XX:MetaspaceSize，(jdk8之前:-XX:PermSize永久代大小，-XX:MaxPermSize永久代最大大小）|类信息、字段信息、方法信息、常量、静态变量、即时编译器编译后的代码缓存等数据|动态分配|垃圾回收器|
	  |直接内存|线程共享|OutOfMemoryError|不受java内存堆大小限制，但受制于机器物理内存大小|java堆的DirectByteBuffer对象指向堆外内存|动态分配|垃圾回收器|
	- 典型特征异常报错及其解决方案
		- java堆典型错误特征
		  1. java.lang.OutOfMemoryError: Java heap space 
		  假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发此错误。
		  解决方案
		  a. jvm打印堆转储快照信息，也就是堆栈信息 -XX:+HeapDumpOnOutOfMemoryError
		  b. 用工具对堆转储快照文件hprof文件进行分析,例如 VisualVM，jprofile 等分析工具
		  c. 分析判断是内存溢出还是内存泄漏
		  内存泄漏:通过工具查询泄漏对象到GC Root引用链，查询分析出泄漏对象是如何泄漏出来的--->实战分析Thread Local泄漏场景
		  内存溢出:如果没有内存泄漏问题，确实是内存溢出，检查java应用堆设置大小（-Xmx与-Xms等）是否满足应用需求，是否需要扩容,
		  需要进一步做各种具体分析:例如减少应用运行期内存消耗,某些对象生命周期是否过长,对象持有时间是否过长，存储结构是否合理等等。
		  
		  2. java.lang.OutOfMemoryError: GC Overhead Limit Exceeded 
		  当 JVM 花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
		  解决方案
		- java方法栈和本地方法典型错误
		  1. java.lang.StackOverflowError:
		  当栈不支持动态扩展（hopspot虚拟机）线程栈空间无法容纳新栈帧时候
		  解决方案
		  
		  2. java.lang.OutOfMemoryError:unable to create native thread:possiblyout of memory or process/resource limits reached
		  线程申请内存空间不足时,可能需要在32位虚拟机上才可复现
		  原因分析:
		  可分配栈容量大小=最大内存容量（32位系统单进程最大2GB）-(堆容量上限+方法区容量上限+程序计数器容量（很小，可忽略不计）+直接内存+虚拟机进程本身内存消耗)
		  可分配栈容量大小=线程数*每个线程栈容量大小
		  解决方案
		  从公式分析可知：简单数学分析就可知道（仅限32位虚拟机，64位虚拟机没有最大进程2GB限制）
		  a. 减少线程数
		  b. 减少堆容量上限大小和方法区容量上限大小,特别是32位系统上减少堆容量上限这点可能有疑惑。
		- 方法区典型错误
		  1. java.lang.OutOfMemoryError: PermGen space
		  jdk6
		  解决方案
		  控制永久代大小-XX:PermSize,-XX:MaxPermSize(jdk8已废弃永久代)
		  2. java.lang.OutOfMemoryError: Metaspace
		  jdk8
		  常见原因:
		  产生大量动态类
		  a. 例如cglib等字节码库生成动态类
		  b.  大量动态生成JSP（JSP第一次运行是需要编译为java类）
		  c. 基于OSGi的应用（即使是同一个类，被不同类加载器加载也被视为不同的类）
		  解决方案
		  控制方法区大小：-XX:MetaspaceSize=10M -XX:MaxMetaspaceSize=10M
		- 本机直接内存异常
		  1. java.lang.OutOfMemoryError
		  特征错误信息:同时HeapDump文件很小，没有明显异常
		  解决方案：而程序中正好使用了DIrectMemory，就可以考虑检查直接内存部分了
- java堆
- 内存泄漏
- 内存溢出
-