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
  java语法
  支持局部变量类型推断(var)
  
  jvm
  垃圾回收器接口
- jdk11(LTS)
  (Long-Term-Support)版本
  
  api实现优化
  HTTP Client 标准化
  
  JVM
  ZGC实验性质
  
  功能
  启动单文件源代码程序
  
  安全:
  TLS1.3 协议
- jdk12
  java语法
  增强 Switch
  ```java
  switch (day) {
      case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
      case TUESDAY                -> System.out.println(7);
      case THURSDAY, SATURDAY     -> System.out.println(8);
      case WEDNESDAY              -> System.out.println(9);
  }
  ```
  instanceof 模式匹配
  ```java
  Object obj = "我是字符串";
  if(obj instanceof String str){
  	System.out.println(str);
  }
  ```
- jdk13
  jdk api实现优化
  SocketAPI 重构
- jdk14
  java语法
  switch 的增强(转正)
  ```java
  String result = switch (day) {
              case "M", "W", "F" -> "MWF";
              case "T", "TH", "S" -> "TTS";
              default -> {
                  if(day.isEmpty())
                      yield "Please insert a valid day.";
                  else
                      yield "Looks like a Sunday.";
              }
  
          };
  System.out.println(result);
  ```
  
  空指针异常精准提示
  a.b.c.i = 99; // 假设这段代码会发生空指针
  -XX:+ShowCodeDetailsInExceptionMessages
  可以在空指针异常中获取更为详细的调用信息，更快的定位和解决问题。
  ```java
  // 增加参数后提示的异常中很明确的告知了哪里为空导致
  Exception in thread "main" java.lang.NullPointerException:
          Cannot read field 'c' because 'a.b' is null.
      at Prog.main(Prog.java:5)
  ```
  
  预览新特性
  record 关键字
-
- jdk15
- jdk16
- jdk17