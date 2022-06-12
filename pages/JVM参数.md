- Java HotSpot VM的官方文档
  没有说明的情况下特指jdk8情况下
- HotSpot虚拟机两类参数类型
  |参数类型|类型|描述|格式|
  |---|---|---|---|
  |-X|非标准参数|不是虚拟机规范规定的,不是所有其他虚拟机都支持这些参数|-Xms20m|
  |-XX|稳定参数|是虚拟机规范规定的|-XX:+PrintFlagsInitial,-XX:FieldsAllocationStyle=0|
  严格区分大小写，不合法的jvm参数，运行时会直接报错，无法识别该jvm参数
  前者小写后者大写
  
  HotSpot虚拟机针对-XX类型参数
  boolean类型:-XX:+/-arg,+表示开启，-表示关闭
- 内存区域大小
  -Xss|栈空间大小(Stock Size) -Xss128k间接控制栈深度
  -XX:StringTableSize|设置字符串常量池大小
  -XX:MaxMetaspaceSize| 标志设置最大元空间大小，默认值为 unlimited，这意味着它只受系统内存的限制。
  -XX:MetaspaceSize| 调整标志定义元空间的初始大小如果未指定此标志，则 Metaspace 将根据运行时的应用程序需求动态地重新调整大小。
  
  -XX:PermSize|jdk8废弃,方法区 (永久代) 初始大小
  -XX:MaxPermSize| jdk8废弃，方法区 (永久代) 最大大小
- 线程相关JVM参数
  id:: 6298a212-7591-4a6d-92e8-743494bfe3f4
  -XX:+UseSpinning|bool|true| 开启自旋锁 jdk1.6，默认true ((f6152514-a942-4aa1-8e61-de1116a30b3c))
  -XX:PreBlockSpin|intx|10| 自选次数,默认10次
  -XX:+UseBiased Locking|bool|true|,开启偏向锁，jdk6后默认开启，但是jdk15默认关闭 
  [JEP 374: Deprecate and Disable Biased Locking](https://openjdk.java.net/jeps/374)
- TLAB
  id:: 6298ce15-1863-4aae-99c9-5d8d7ae4dfdb
  -XX:+/-UseTLAB|bool|true|默认开启TLAB
- 垃圾回收相关
  -XX:MaxTenuringThreshold|intx|15|对象进入老年代的年龄阈值
  -XX:TargetSurvivorRatio|intx|50|设定survivor区的目标使用率。默认50，即survivor区对象目标使用率为50%。
- 打印
  -XX:+PrintTenuringDistribution|bool|false|打印出当次 GC 后的 晋升老年代的年龄阈值
- 垃圾回收器
  -XX:+UseParallelGC|bool|false|使用Parallel Scavenge垃圾回收器处理新生代,对老生代不生效，默认禁用|
  -XX:+UseParallelOldGC|bool|true|使用Parallel Old垃圾回收器，默认启用
- 内存异常
  -XX:+HeapDumpOnOutOfMemoryError|bool|false|内存溢出异常时打印堆转储快照信息