- 目的：解决多个线程访问资源的同步性问题。
- 作用:保证同一个时刻synchronized修饰的方法或者方法块只有一个线程能执行。(互斥锁)
  可以修饰静态方法，实例方法，方法块
- jvm不同版本对synchronized锁的优化
  id:: f6152514-a942-4aa1-8e61-de1116a30b3c
  	1. 早期就是个重量级锁,效率很低。
  	2. jdk6之后jvm对synchronized锁进行了一系列优化，自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等。
     自旋锁:CAS机制，注意自旋等待时间，时间太长反而影响CPU执行效率。jvm可以设置是否开启自旋和自旋次数 ((6298a212-7591-4a6d-92e8-743494bfe3f4)) 
     自适应自旋锁:对自旋锁的优化,
   你会发现目前的话，不论是各种开源框架还是 JDK 源码都大量使用了 synchronized 关键字。--->jdk8的ConcurrentHashMap源码线程安全就是CAS+synchronized了(相比jdk7)。
- synchronized的底层原理
  id:: 629875cb-0dcd-4aff-a6f6-520d1fbfcb63
	- 0.背景知识
	  Class文件格式，对象头MarkWord
	- 1.jvm层理解synchronized
	  实例synchronized修饰同步块
	  ```
	  public class SynchronizedDemo {
	      public void method() {
	          synchronized (this) {
	              System.out.println("synchronized 代码块");
	          }
	      }
	  }
	  ```
	  执行反编译
	  ```shell
	  javap -c -s -v -l SynchronizedDemo.class
	  ```
	  ![image.png](../assets/image_1654159626872_0.png) 
	  
	  核心jvm指令monitorenter , monitorexit
	  
	  当执行 monitorenter 指令时，线程试图获取锁也就是获取 对象监视器 monitor 的持有权。
	  对象监视器 monitor同一时刻只允许有一个拥有者。
	  
	  下面同时解释了synchronized为何是**可重入锁**
	  monitorenter:
	  1. 如果对象监视器monitor的锁计数器为0,则线程允许进入代码块,锁计数器+1。这个线程就拥有了这个锁
	  2. 如果该线程本身已经拥有了这个锁，那么锁计数器+1.
	  3. 如果其他线程已经拥有了这个锁，那个该线程则阻塞直到锁计数器为0才再次尝试
	  monitorexit:
	  1.对象监视器monitor的锁计数器-1,如果锁计数器为0，则说明该线程不再拥有该锁，其他因锁阻塞的线程可以开始抢占锁了。如果不为0,则该线程仍持有该锁。
	  
	  [jvm8-monitorenter官方文档](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.monitorenter)
	  
	  注意：
	  同步代码块是通过 monitorenter 和 monitorexit 来实现的，
	  同步方法是加了一个 ACC_SYNCHRONIZED 修饰来实现的，
	  不过两者底层本质还是对象监视器 monitor 。
	  
	  更底层就是看jdk用C++源码怎么实现的了
	  Monitor 是基于 C++实现的，由ObjectMonitor实现的。每个对象中都内置了一个 ObjectMonitor对象。
	-
	- 2. 锁的四种状态
	  无锁状态--->偏向锁状态--->轻量级锁状态--->重量级锁状态
	  锁可以升级，但不能降级。
	- 3.锁的升级化过程
	  
	  [从jdkC++源码级别分析总结了锁的升级化过程,有图很详细-有赞技术团队](https://tech.youzan.com/javasuo-yu-xian-cheng-de-na-xie-shi/)
	  ![2.6本章小结锁的升级化过程图.png](../assets/image_1654158066220_0.png){:height 482, :width 790}
- 典型场景应用
  2. 双重锁检测方式实现单例模式
  2. 双重锁检测方式实现
   ``` java
   public class Singleton {
- private volatile static Singleton uniqueInstance;
- private Singleton() {
   }
- public  static Singleton getUniqueInstance() {
   //先判断对象是否已经实例过，没有实例化过才进入加锁代码
   if (uniqueInstance == null) {
   //类对象加锁
   synchronized (Singleton.class) {
   if (uniqueInstance == null) {
   uniqueInstance = new Singleton();
   }
   }
   }
   return uniqueInstance;
   }
   }
   ```
- 资料
  [Java锁的升级化过程分析，有图很详细-有赞技术团队](https://tech.youzan.com/javasuo-yu-xian-cheng-de-na-xie-shi/)