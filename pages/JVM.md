- [[java内存模型(JMM)]]
- [[java内存区域]]
- [[jvm对象堆内存布局]]
- 对象访问方式
  主要有两种对象访问方式
	- 1. 直接指针方式
	  hotspot虚拟机采用直接指针方式
	  数据结构定义：对象引用直接指向对象地址，对象内存布局里需要额外存储对象类型指针
	  优点:对象访问速度快，相比句柄方式而言节省了一次指针定位过程
	  缺点:
	- 2. 句柄访问方式
	  数据结构定义:对象引用指向句柄，对象直接指针和对象类型指针组合在一起，形成句柄数据结构
	  优点:对象引用存储的是稳定句柄地址不会变化，(垃圾回收器回收内存，例如压缩内存，对象地址可能会发生变化，变的也只会是句柄结构中的对象地址)
	  缺点:
- [[类加载机制]]
- [[JVM参数]]
- 四大引用类型
  id:: 6299ed85-5fab-4abb-ad7e-d901f1d30469
  强引用
  软引用(SoftReference)
  弱引用(WeakReference)
  虚引用(PhantomReference)
  ![截屏2022-06-03 下午7.21.04.png](../assets/截屏2022-06-03_下午7.21.04_1654255291803_0.png)
  Reference
  当引用类型Reference构造器传入的类型referent对象被gc垃圾回收器回收时，再调用Reference的get方法会返回null。
  ```java
  public abstract class Reference<T> {
  	private T referent;
  	volatile ReferenceQueue<? super T> queue;
  	Reference(T referent) {
          this(referent, null);
      }
  
      Reference(T referent, ReferenceQueue<? super T> queue) {
          this.referent = referent;
          this.queue = (queue == null) ? ReferenceQueue.NULL : queue;
      }
  	/**
       * Returns this reference object's referent.  If this reference object has
       * been cleared, either by the program or by the garbage collector, then
       * this method returns <code>null</code>.
       *
       * @return   The object to which this reference refers, or
       *           <code>null</code> if this reference object has been cleared
       */
      public T get() {
          return this.referent;
      }
  }
  ```
  应用:
  ThreadLocal的ThreadLocalMap的Entry就是继承了WeakReference，导致可能存在的潜在内存泄漏问题。
-
- 优化
  逃逸分析技术
  应用 锁消除 ((6298a5bf-d69e-468c-816f-036e2d687654))
- 资料
  ((628ca746-598e-4db5-b964-7c4a80b26680))
- 书籍
  《深入理解 Java 虚拟机：JVM 高级特性与最佳实践 》第三版周志明