- 主要步骤
  初始化
  hash
  扩容--->rehash
  hash冲突
  链表<->红黑树
  put
  get
  remove
- 数据结构
  版本差异:jdk8之前与jdk之后
  jdk7：Segment 数组 + HashEntry 数组 + 链表 
  jdk8:Node 数组 + 链表 / 红黑树
- 线程安全
  jdk7:分段锁，也就是每一个 Segment 上同时只有一个线程可以操作
  jdk8:Synchronized 锁加 CAS
  其实 Synchronized 锁自从引入锁升级策略后，性能不再是问题，有兴趣的同学可以自己了解下 Synchronized 的锁升级。
- TODO:cj 等并发看完回头梳理