- 命令行工具
	- 1. jps(JVM Process Status）
	  jps (JVM Process Status）: 类似 UNIX 的 ps 命令。用于查看所有 Java 进程的启动类、传入参数和 Java 虚拟机参数等信息；
	  jps：显示虚拟机执行主类名称以及这些进程的本地虚拟机唯一 ID（Local Virtual Machine Identifier,LVMID）
	  jps -q ：只输出进程的本地虚拟机唯一 ID
	  jps -l:输出主类的全名，如果进程执行的是 Jar 包，输出 Jar 路径。
	  jps -v：输出虚拟机进程启动时 JVM 参数。
	  jps -m：输出传递给 Java 进程 main() 函数的参数。
	  ```shell
	  jps -lvm
	  ```
	-
	- 2. jstat（JVM Statistics Monitoring Tool）
	  用于收集 HotSpot 虚拟机各方面的运行数据;
	- 3. jinfo(Configuration Info for Java)
	  Configuration Info for Java,显示虚拟机配置信息;
	- 4. jmap (Memory Map for Java)
	  生成堆转储快照;
	- 5. jhat (JVM Heap Dump Browser) 
	  用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果;
	- 6. jstack (Stack Trace for Java)
	  生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。
-