- [[java内存模型(JMM)]]
  java内存区域
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
- 优化
  逃逸分析技术
  应用 锁消除 ((6298a5bf-d69e-468c-816f-036e2d687654))
- 资料
  ((628ca746-598e-4db5-b964-7c4a80b26680))