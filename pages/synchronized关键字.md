- 目的：解决多个线程访问资源的同步性问题。
- 作用:保证同一个时刻synchronized修饰的方法或者方法块只有一个线程能执行。(互斥锁)
  可以修饰静态方法，实例方法，方法块
- jvm不同版本对synchronized锁的优化
  	1. 早期就是个重量级锁,效率很低。
  	2. jdk6之后jvm对synchronized锁进行了一系列优化，自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等。
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
	  
	  jvm指令monitorenter , monitorexit
	  
	  monitorenter:
	  monitorexit:
	  [jvm8-monitorenter官方文档](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.monitorenter)
	  
	  更底层就是看jdk用C++源码怎么实现的了
	  Monitor 是基于 C++实现的，由ObjectMonitor实现的。每个对象中都内置了一个 ObjectMonitor对象。
	-
	- 2. 锁的四种状态
	  无锁状态--->偏向锁状态--->轻量级锁状态---->重量级锁状态
	  逐步加深
	- 3.锁的升级化过程
	  
	  图片整理梳理得很不错,后续再看看
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