- AQS AbstractQueuedSynchronizer抽象队列同步器是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出大量应用广泛的同步器
  例如ReentrantLock，Semaphore，ReentrantReadWriteLock,CountDownLatch等
  CycliBarrier是基于ReentrantLock,
  
  我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。
- 常见类
  注意StampedLock jdk1.8不是基于AQS设计的
  ![截屏2022-06-05 下午9.04.20.png](../assets/截屏2022-06-05_下午9.04.20_1654434285393_0.png)
- 核心类:
- 设计模式
  同步器的设计是基于模板方法模式的,
  AbstractQueuedSynchronizer以下5个方法必须被子类实现，默认抛出UnsupportedOperationException异常
  其他方法都是final方法,无法修改。
  ```java
  protected boolean tryAcquire(int)//独占方式。尝试获取资源，成功则返回true，失败则返回false。
  protected boolean tryRelease(int)//独占方式。尝试释放资源，成功则返回true，失败则返回false。
  protected int tryAcquireShared(int)//共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
  protected boolean tryReleaseShared(int)//共享方式。尝试释放资源，成功则返回true，失败则返回false。
  protected boolean isHeldExclusively()//该线程是否正在独占资源。只有用到condition才需要去实现它。
  ```
  自定义的同步器类继承AbstractQueuedSynchronizer，必须实现一下5个方法
- AQS原理
  AbstractQueuedSynchronizer.Sync
- 使用场景
  1. jdk源码例如ReentrantLock，Semaphore，ReentrantReadWriteLock,CountDownLatch等
  CycliBarrier是基于ReentrantLock,
  2. 我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。
-
-