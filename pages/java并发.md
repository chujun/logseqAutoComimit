- # 背景知识
  id:: 6298181c-7741-4944-ab3c-e0b6e63b53c4
	- 1. [[java内存模型(JMM)]]
	- 2. CPU缓存模型
	  ((6299badd-eb69-4311-83f8-e55c788c01be))
	- 100. 线程调度方式
	  抢占式调度：
	  协同式调度：
	  java 使用的线程调使用抢占式调度, Java 中线程会按优先级分配 CPU 时间片运行， 且优先级越高越优先执行，但优先级高并不代表能独自占用执行时间片，可能是优先级高得到越多的执行时间片，反之，优先级低的分到的执行时间少但不会分配不到执行时间。
	- 101.死锁
	  **哲学家进餐问题**
	  ![image.png](../assets/image_1654151519921_0.png){:height 406, :width 835} 
	  死锁产生的4个条件：
	  1.互斥条件：该进程请求的资源必须是互斥使用的。--->哲学家中的筷子,打印机设备，而内存不是互斥的。不会有死锁问题。
	  2.请求与保持条件：一个进程至少获得了一个资源，因请求其他资源而阻塞时，对已获得的资源保持不放。
	  3.不剥夺条件:进程已获得的资源在未使用完之前不能被其他进程强行剥夺，只有自己使用完毕后才释放资源。
	  4.循环等待条件:存在一种进程对资源的**循环等待链(循环链)**,链中的每一个进程已获得的资源被下一个进程请求。
	  
	  如何预防死锁?破坏死锁的产生的必要条件即可：
	  破坏请求与保持条件 ：一次性申请所有的资源。
	  破坏不剥夺条件 ：占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。--->应用：分布式锁的超时释放机制
	  破坏循环等待条件 ：靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。
	  
	  
	  在资源分配时，借助于算法（比如银行家算法）对资源分配进行计算评估，使其进入安全状态。
	  
	  >安全状态 指的是系统能够按照某种线程推进顺序（P1、P2、P3.....Pn）来为每个线程分配所需资源，直到满足每个线程对资源的最大需求，使每个线程都可顺利完成。称<P1、P2、P3.....Pn>序列为安全序列。
	  死锁的检测和解除：
- # 基础知识点
	- 进程和线程
	  程序执行的基本单元,一个进程可以有多个线程，
	  线程是更小的CPU执行单位
	  线程间的切换和调度的成本远远小于进程。
	  外加协程
	  两者之间区别，联系？
	  JVM角度分析
	  多个线程共享进程的堆和方法区(jdk8元空间)，每个线程有自己的虚拟机栈和本地方法栈，程序计数器(java内存区域)
	- 线程的生命周期和状态
	  1. 线程状态
	  ![image.png](../assets/image_1654136820182_0.png)
	  注意点:在操作系统中层面线程有 READY 和 RUNNING 状态，而在 JVM 层面只能看到 RUNNABLE 状态
	- 2. 线程生命周期图
	  图就是最好的说明
	  ![image.png](../assets/image_1654136846423_0.png)
	  原图中 wait 到 runnable 状态的转换中，join实际上是Thread类的方法，但这里写成了Object。
	-
	- 线程让出CPU资源情况：
	  1. 时间片用完(线程调度机制决定的)  
	  2. 主动让出 CPU，比如调用了 sleep(), wait() , 获取不到锁等(线程生命周期图里的几种情况)
	  3. 调用了阻塞类型的系统中断，比如请求 BIO，线程被阻塞。
	  5. 被终止或结束运行
	- 线程上下文切换
	  需要保存当前线程的上下文，留待线程下次占用 CPU 的时候恢复现场。并加载下一个将要占用 CPU 的线程上下文。
- # 多线程
  1. 为什么使用多线程
  硬件角度：CPU单核CPU->多核CPU,充分利用CPU资源
  操作系统角度：线程是CPU执行的基本单元，线程间的上下文切换的成本远远小于进程
  互联网发展角度:满足系统设计高并发的需求
  2. 使用多线程会碰到哪些问题？
  死锁:多个线程同时被阻塞，它们中的一个或者全部都在等待某一个资源被释放。线程被无限期地阻塞。
  ![image.png](../assets/image_1654138306792_0.png) 
  内存泄漏(ThreadLocal内存泄漏)
  线程不安全，
  线程间通信(线程同步，线程之间消息传递)
  
  对应用程序开发来说，并发编程更复杂,更容易出错
- 创建线程
-
- 线程Thread基本方法
	- 实现 Runnable 接口和 Callable 接口的区别
	  1. Runnable接口不会返回结果或抛出检查异常。
	  2. Callable接口可以返回结果或者抛出异常。
	  
	  工具类 Executors 可以实现将 Runnable 对象转换成 Callable 对象。
	  （Executors.callable(Runnable task) 或 Executors.callable(Runnable task, Object result)）。
	- wait, notify, notifyAll
	- 说说 sleep() 方法和 wait() 方法区别和共同点?
	  两者都释放CPU资源，但sleep不释放锁资源，而wait释放锁资源(必须在sync)
	  两者最主要的区别在于：sleep() 方法没有释放锁，而 wait() 方法释放了锁 。
	  两者都可以暂停线程的执行。
	  wait() 通常被用于线程间交互/通信，sleep() 通常被用于暂停执行。
	  wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或者 notifyAll() 方法。sleep() 方法执行完成后，线程会自动苏醒。或者可以使用 wait(long timeout) 超时后线程会自动苏醒。
	- yield方法和join方法区别
	  yield方法就是让出CPU资源，
	  而join方法底层是基于wait方法实现的,本质就是对wait方法使用的简化包装
	- 为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？
	  start方法调用内核系统调用启动了新线程,这会有多个线程，而直接调用run方法不会产生新线程，就只是个普通方法调用。
	- 中断相关方法
	  ```java
	  	/**
	  	* 中断线程
	      */
	  	public void interrupt() {
	          if (this != Thread.currentThread())
	              checkAccess();
	  
	          synchronized (blockerLock) {
	              Interruptible b = blocker;
	              if (b != null) {
	                  interrupt0();           // Just to set the interrupt flag
	                  b.interrupt(this);
	                  return;
	              }
	          }
	          interrupt0();
	      }
	  	/**
	  	* 判断线程是否中断,不清除中断状态，
	  	* 重载方法
	      */
	  	public static boolean interrupted() {
	          return currentThread().isInterrupted(true);
	      }
	  	/**
	  	*判断线程是否中断,不清除中断状态,
	  	* 重载方法
	      */
	  	public boolean isInterrupted() {
	          return isInterrupted(false);
	      }
	  	/**
	  	*判断线程是否中断，根据传入的Boolean值判断是否要清除中断状态
	      */
	  	private native boolean isInterrupted(boolean clearInterrupted);
	  ```
- # [[synchronized关键字]]
-
- # volatile 关键字
  volatile
	- 背景知识
	  1. [[java内存模型(JMM)]]
	- volatile保证两个特性：
	  内存可见性
	  禁止指令重排序
	- volatile修饰变量在并发环境下是不安全的
	  因为java运算不能保证原子性,没有满足锁的原子性要求,例如a++
	- volatile与synchronized关键字区别
	  1. volatile是轻量级实现,volatile 性能肯定比synchronized关键字要好 .
	  volatile 关键字只能用于变量而 synchronized 关键字可以修饰方法以及代码块 .
	  2. volatile只保证内存可见性,不能保证数据的原子性,synchronized 关键字两者都能保证。
	  3. volatile关键字主要用于解决变量在多个线程之间的可见性，而 synchronized 关键字解决的是多个线程之间访问资源的同步性。
	- volatile底层实现原理
	  TODO:cj 后续补充
	- 应用：
		- 1. volatile+ Unsafe的CAS机制 实现锁功能
		  juc包里大量使用，例如原子类包AtomicInteger
		- 2. 双重锁检测实现单例模式
		  ((629865a4-5176-4fe4-87f9-46a1ff1883e3))
- CAS机制
  id:: 629dbe40-7798-4e22-a293-dadfeb09e50b
	- 定义:CAS 的原理是拿期望的值和原本的一个值作比较，如果相同则更新成新的值。
	  乐观锁的一种实现方式
	- CAS实现原理
	  基于Unsafe的CAS方法，compareAndSwapObject,compareAndSwapInt,compareAndSwapLong
	  [[Unsafe]]
	  ```java
	  public final native boolean compareAndSwapObject(Object obj, long fieldOffset, Object expectedValue, Object newValue);
	  
	      public final native boolean compareAndSwapInt(Object obj, long fieldOffset, int expectedValue, int newValue);
	  
	      public final native boolean compareAndSwapLong(Object obj, long fieldOffset, long expectedValue, long newValue);
	  
	  ```
	  查看下AtomicInteger的源码
	  AtomicInteger 类主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作.
	  UnSafe 类的 objectFieldOffset() 方法是一个本地方法，这个方法是用来获取指定Field字段的内存地址。
	  ```java
	  // setup to use Unsafe.compareAndSwapInt for updates（更新操作时提供“比较并替换”的作用）
	  public class AtomicInteger extends Number implements java.io.Serializable {
	      private static final long serialVersionUID = 6214790243416807050L;
	  
	      // setup to use Unsafe.compareAndSwapInt for updates
	      private static final Unsafe unsafe = Unsafe.getUnsafe();
	      private static final long valueOffset;
	  
	      static {
	          try {
	              valueOffset = unsafe.objectFieldOffset
	                  (AtomicInteger.class.getDeclaredField("value"));
	          } catch (Exception ex) { throw new Error(ex); }
	      }
	  
	      private volatile int value;
	  }
	  ```
	- ABA问题
	- ABA问题解决
	  AtomicStampedReference:原子更新带有版本号的引用类型
- # java锁的的实现方式
  java锁
	- 实现锁的三大要求
	  id:: 6299c682-4f78-4d5f-9d9c-2e032d7d8e8f
	  原子性:一次操作或者多次操作，要么所有的操作全部都得到执行,要么都不执行
	  内存可见性:当一个线程对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值。volatile 关键字可以保证共享变量的内存可见性。
	  有序性:代码在执行的过程中的先后顺序，Java 在编译器以及运行期间的优化，代码的执行顺序未必就是编写代码时候的顺序。volatile 关键字可以禁止指令进行重排序优化。
	  ((629c2156-6c97-400d-865b-80a8f71be9b0))
	- java加锁的三种方式
	  java加锁三种方式实现
		- 1. synchronized
		  JVM层面源语实现
		  可重入锁，非公平锁,不可中断锁
		- 2. volatile+ Unsafe的CAS机制无锁化加锁方案
		  而CAS保证了操作原子性,volatile保证内存可见性和禁止指令重排序,
		  不加volatile的话,可能变更的值不会给其他线程立刻可见.
		- 3. Lock API
		  以ReentrantLock举例是可重入锁
	- 加锁方式比较
	  ReentrantLock与synchronized区别
		- a. 支持中断锁。
		  中断锁:中断锁指的是锁在执行时可被中断，也就是在执行时可以接收 interrupt 的通知，从而中断锁执行。
		  lockInterruptibly()方法 抛出InterruptedException异常
		  中断锁应用场景:
		- b. 公平锁和非公平锁都支持
		- c. 锁可以绑定多个条件
		  同时绑定多个Condition对象，只需多次调用newCondition方法即可。
		  例如ArrayBlockingQueue队列中的notEmpty,notFull Condition
		  ```java
		  public ArrayBlockingQueue(int capacity, boolean fair) {
		        if (capacity <= 0)
		            throw new IllegalArgumentException();
		        this.items = new Object[capacity];
		        lock = new ReentrantLock(fair);
		        notEmpty = lock.newCondition();
		        notFull =  lock.newCondition();
		    }
		  ```
		  手动lock，finally unlock实现，
		  ![image.png](../assets/image_1654756996301_0.png)
- 锁的分类
	- 锁的升级:无锁状态,偏向锁,轻量级锁和重量级锁
	- 自旋锁
	- 分布式锁
	- 可重入锁和非可重入锁
	- 公平锁和非公平锁
	  id:: 62a01030-1752-4dfb-a54c-606c810eb3b3
	  公平锁:保证线程先到先得
	  公平,效率低
	  非公平锁:不保证线程先到先得,竞争获得，
	  效率高,但也有可能线程一直获取不到锁资源，处于"饥饿"状态
	- 乐观锁和悲观锁
	  乐观锁:先乐观假设没有锁冲突而去完成某项操作，如果因为锁冲突失败就重试，直到成功为止
- [[java并发包]]
- 线程池
	- 背景知识
	  1. 池化技术
	  主要目的:减少每次获取资源的消耗，提高对资源的利用率。
	  应用:线程池、数据库连接池、Http 连接池
	- 使用线程的好处:
	  1. 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
	  2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
	  3. 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。
	- [[java线程池]]
- [[线程单例]]
- 伪共享
-