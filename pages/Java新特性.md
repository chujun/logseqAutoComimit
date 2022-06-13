- jdk8
  函数式编程
  Lambda 表达式
  Interface default方法
  Stream
  Optional
  Date-Time API
  LocalDateTime，LocalDate，LocalTime
  CompletableFuture
  StampedLock
- jdk9
  命令行工具
  JShell
  
  功能
  模块化系统
  
  JVM
  G1成为默认垃圾回收器
  
  api实现优化
  String 存储结构优化
  Java 8 及之前的版本，String 一直是用 char[] 存储。在 Java 9 之后，String 的实现改用 byte[] 数组存储字符串，节省了空间
  
  Java API
  进程 API:java.lang.ProcessHandle
  变量句柄:VarHandle
  变量句柄是一个变量或一组变量的引用，包括静态域，非静态域，数组元素和堆外数据结构中的组成部分等。
  变量句柄的含义类似于已有的方法句柄 MethodHandle ，由 Java 类 java.lang.invoke.VarHandle 来表示，可以使用类 java.lang.invoke.MethodHandles.Lookup 中的静态工厂方法来创建 VarHandle 对象。
  VarHandle 的出现替代了 java.util.concurrent.atomic 和 sun.misc.Unsafe 的部分操作。并且提供了一系列标准的内存屏障操作，用于更加细粒度的控制内存排序。在安全性、可用性、性能上都要优于现有的 API。
  
  
  java语法
  接口私有方法
  try-with-resources 增强
-
-
-
-
- jdk10
- jdk11
- jdk12
- jdk13
- jdk14
- jdk15
- jdk16
- jdk17