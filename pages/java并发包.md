- 里面提供一系列并发开发的工具类,并发包 java.util.concurrent
- Callable,Future
	- Callable:
	- Future:
	  id:: 629b51bd-72dd-460f-a97e-806b99d0e708
	  Future 对象可以判断线程任务是否执行成功，
	  并且可以通过 Future 的 get()方法来获取返回值，get()方法会阻塞当前线程直到任务完成，
	  而使用 get(long timeout，TimeUnit unit)方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。
- Lock,Condition
  LockSupport
  AbstractQueuedSynchronizer(AQS)
  [[AQS AbstractQueuedSynchronizer]]
  ReentrantLock
  StampedLock(jdk1.8)
- 原子类
  id:: 629985ca-1cb9-447c-9919-e0adac191863
	- 定义:原子类说简单点就是具有原子/原子操作特征的类。
	  例如:AtomicInteger,AtomicBoolean，AtomicLong，AtomicLongArray等等
	- 原理
	  基于循环CAS+volatile机制实现同步加锁功能
	- 类型
	  基本类型
	  AtomicInteger，AtomicLong，AtomicBoolean
	  数组类型
	  AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray
	  引用类型
	  AtomicReference：引用类型原子类
	  AtomicStampedReference:原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。
	  TODO:cj 待总结CAS机制
	  AtomicMarkableReference:原子更新带有标记位的引用类型
	  对象的属性修改类型
	  AtomicIntegerFieldUpdater:原子更新整型字段的更新器
	  AtomicLongFieldUpdater:原子更新长整型字段的更新器
	  AtomicReferenceFieldUpdater:原子更新引用类型字段的更新器
	- ![截屏2022-06-04 下午9.32.15.png](../assets/截屏2022-06-04_下午9.32.15_1654349548197_0.png)
	-
- 线程安全并发容器
  Map
  ConcurrentHashMap
  ConcurrentSkipListMap：基于跳表数据结构实现
  
  Queue
  BlockingQueue:接口,阻塞队列
  ArrayBlockingQueue:基于数组，阻塞队列
  ConcurrentLinkedQueue:基于链表，非阻塞队列
  Set
  ConcurrentSkipListSet
  
  List
  CopyOnWriteArrayList:适合读多写少场景
- [[java线程池]]
  id:: 629986de-7a5f-4d94-9345-35be7b205ca6
	-
- ForkJoin框架
- CompletableFuture
  [CompletableFuture原理与实践-外卖商家端API的异步化-美团技术团队](https://mp.weixin.qq.com/s/GQGidprakfticYnbVYVYGQ)
- CountDownLaunch,CyclicBarrier,Semaphore
  类比理解
  CountDownLatch:F4赛车停下来等换好了4个轮胎后再跑，一直跑下去(countdown降为0后，永远不会reset回去)
  CyclicBarrier:王者5排发车，一波发车完，下一波继续等5排再发车
  Semaphore信号量就是限流，一个餐馆20个座位，最多20人同时吃饭
-
-