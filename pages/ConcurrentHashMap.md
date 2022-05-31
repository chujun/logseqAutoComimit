- 主要步骤
  初始化
  hash
  扩容--->rehash
  链表<->红黑树
  put
  get
  remove
- 数据结构
  版本差异:jdk8之前与jdk之后
  jdk7：Segment 数组 + HashEntry 数组 + 链表 
  jdk8:Node 数组 + 链表 / 红黑树
- 线程安全
  Synchronized 锁加 CAS
- TODO:cj 回头梳理