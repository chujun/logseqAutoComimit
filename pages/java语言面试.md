- 基础
  0. java规范
  [Java SE Specifications java规范官方文档链接](https://docs.oracle.com/javase/specs/index.html)
  [java语言规范8官方文档链接](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)
  [jvm规范8官方文档链接](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)
  
  1. java方法调用形参是值传递还是引用传递? 
  方法实参和形参
  明确值传递和引用传递定义。
  值传递:方法接收的是实参值的拷贝。
  引用传递:方法接收的直接是实参所引用的对象在堆中的地址，不会创建副本，对形参的修改将影响到实参。
  引用类型形参存储的值是对象的引用地址。
  无论是基本类型和是引用类型，在实参传入形参时，都是值传递。Java 中只有值传递，
  C语言有值传递和引用传递两种方式
  [Java 中只有值传递](https://github.com/Snailclimb/JavaGuide/blob/main/docs/java/basis/why-there-only-value-passing-in-java.md)
  [这篇介绍更详细清晰,如果还是比较迷惑的话](https://segmentfault.com/a/1190000016773324)
- 集合
- 并发
- JVM
- 新特性