- 线程相关
  id:: 6298a212-7591-4a6d-92e8-743494bfe3f4
  -XX:+UseSpinning|bool|true| 开启自旋锁 jdk1.6，默认true ((f6152514-a942-4aa1-8e61-de1116a30b3c))
  -XX:PreBlockSpin|intx|10| 自选次数,默认10次
  -XX:+UseBiased Locking|bool|true|,开启偏向锁，jdk6默认开启，但是jdk15默认关闭 
  [JEP 374: Deprecate and Disable Biased Locking](https://openjdk.java.net/jeps/374)
-