- 内存区域大小
  -Xss|栈空间大小(Stock Size) -Xss128k间接控制栈深度
  -XX:MaxMetaspaceSize| 标志设置最大元空间大小，默认值为 unlimited，这意味着它只受系统内存的限制。
  -XX:MetaspaceSize| 调整标志定义元空间的初始大小如果未指定此标志，则 Metaspace 将根据运行时的应用程序需求动态地重新调整大小。
  
  -XX:PermSize|jdk8废弃,方法区 (永久代) 初始大小
  -XX:MaxPermSize| jdk8废弃，方法区 (永久代) 最大大小
- 线程相关JVM参数
  id:: 6298a212-7591-4a6d-92e8-743494bfe3f4
  -XX:+UseSpinning|bool|true| 开启自旋锁 jdk1.6，默认true ((f6152514-a942-4aa1-8e61-de1116a30b3c))
  -XX:PreBlockSpin|intx|10| 自选次数,默认10次
  -XX:+UseBiased Locking|bool|true|,开启偏向锁，jdk6默认开启，但是jdk15默认关闭 
  [JEP 374: Deprecate and Disable Biased Locking](https://openjdk.java.net/jeps/374)
-