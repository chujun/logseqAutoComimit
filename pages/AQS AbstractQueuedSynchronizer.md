- AQS AbstractQueuedSynchronizer是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出大量应用广泛的同步器
  例如ReentrantLock，Semaphore，ReentrantReadWriteLock
  我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。
  
  核心类:AbstractQueuedSynchronizer.Sync
- AQS原理