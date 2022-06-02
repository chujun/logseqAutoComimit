- 构造者模式
- 单例模式
  2. 双重锁方式
  
  ```java
  public class Singleton {
  
      private volatile static Singleton uniqueInstance;
  
      private Singleton() {
      }
  
      public  static Singleton getUniqueInstance() {
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
  为什么uniqueInstance必须用volatile关键字修饰
  线程安全问题
  原因:uniqueInstance = new Singleton(); 这段代码其实是分为三步执行：
  1. 为 uniqueInstance 分配内存空间
  2. 初始化 uniqueInstance
  3. 将 uniqueInstance 指向分配的内存地址.
   JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。虽然这种概率很小很小
-