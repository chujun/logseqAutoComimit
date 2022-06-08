- AQS AbstractQueuedSynchronizer抽象队列同步器是一个用来构建锁和同步器的框架，使用 AQS 能简单且高效地构造出大量应用广泛的同步器
  例如ReentrantLock，Semaphore，ReentrantReadWriteLock,CountDownLatch等
  CycliBarrier是基于ReentrantLock,
  
  我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。
- 基于AQS实现的常见线程类
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
- AQS核心思想
  如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。
  如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，
  这个机制 AQS 是用 CLH 队列锁实现的，即将暂时获取不到锁的线程加入到队列中。
  
  请求释放锁的共享资源时，共享资源为为非锁定状态,则唤醒CLH队列锁的线程竞争共享资源
  ((62a005a5-bf25-40b9-a19d-41df701c9d49))
- AQS 对资源的两种共享方式
  1. Exclusive(独占方式）
  同一时刻只有一个线程能执行, 例如ReentrantLock
  锁又分为公平锁和非公平锁
  公平锁:保证先到先得,按照线程在队列中的排队顺序，先到者先拿到锁
  非公平锁:不保证先到先得，当线程要获取锁时，先通过CAS 操作去抢锁，如果没抢到，当前线程再加入到队列中等待唤醒。
  ((62a0110a-97d3-4970-822d-38900c515148)) 
  
  2. Share(共享方式)
  多个线程可同时执行，例如Semaphore,CountDownLatch,CyclicBarrier、ReadWriteLock
  
  一般锁和同步器都是一种共享方式，
  而ReentrantReadWriteLock可以看成是组合式，因为 ReentrantReadWriteLock 也就是读写锁允许多个线程同时对某一资源进行读。
-
- AQS原理
- AQS源码分析
  同步状态字段state
  使用volatitle int 成员变量来表示同步状态
  ```java
  private volatile int state;//共享变量，使用volatile修饰保证线程可见性
  /返回同步状态的当前值
  protected final int getState() {
          return state;
  }
   // 设置同步状态的值
  protected final void setState(int newState) {
          state = newState;
  }
  //原子地（CAS操作）将同步状态值设置为给定值update如果当前同步状态的值等于expect（期望值）
  protected final boolean compareAndSetState(int expect, int update) {
          return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
  }
  ```
- CLH队列
  id:: 62a005a5-bf25-40b9-a19d-41df701c9d49
  定义:虚头节点的双链阻塞队列 ,FIFO,AQS 是将每条请求共享资源的线程封装成一个 CLH队列的一个结点（Node）来实现锁的分配。
  一般使用自旋锁spinLock
  相比普通的双链队列有什么特点?
  ![AQS CLH队列.png](../assets/AQS_CLH队列_1654654446145_0.png)
- JDK实现AQS的类
  CountDownLauch的共享资源state数量由构造器指定,ReentrantLock的共享资源state数量为1
  AbstractQueuedSynchronizer.Sync
- 子类实现AQS
  自定义同步器在实现时只需要实现共享资源 state 的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS 已经在上层已经帮我们实现好了。
  要求:一般子类实现方法都需要操作共享状态变量state值
	- 利用AQS实现共享模式和独占模式
	  自定义同步器是独占模式时一般覆盖tryAcquire和tryRelease即可,
	  而是共享式模式时覆盖tryAcquireShared和tryReleaseShared方法即可
	  特别的ReentrantReadWriteLock
	- 利用AQS如何设计实现公平锁
	  id:: 62a01247-baa8-4152-b470-821a6ed112f5
	  AQS源码文档中提到一种实现公平锁的通常方式，再实现tryAcquire方法时,判断hasQueuedPredecessors方法如果返回true,则tryAcquire返回false。
	  ReentrantLock类就是这么实现公平锁的。
	  ```java
	  //ReentrantLock.FairSync
	  		protected final boolean tryAcquire(int acquires) {
	              final Thread current = Thread.currentThread();
	              int c = getState();
	              if (c == 0) {
	                  if (!hasQueuedPredecessors() &&
	                      compareAndSetState(0, acquires)) {
	                      setExclusiveOwnerThread(current);
	                      return true;
	                  }
	              }
	              else if (current == getExclusiveOwnerThread()) {
	                  int nextc = c + acquires;
	                  if (nextc < 0)
	                      throw new Error("Maximum lock count exceeded");
	                  setState(nextc);
	                  return true;
	              }
	              return false;
	          }
	  ```
- 自定义子类实现AQS实战
- 使用场景
  1. jdk源码例如ReentrantLock，Semaphore，ReentrantReadWriteLock,CountDownLatch等
  CycliBarrier, SynchronousQueue是基于ReentrantLock,
  2. 我们自己也能利用 AQS 非常轻松容易地构造出符合我们自己需求的同步器。
-
-