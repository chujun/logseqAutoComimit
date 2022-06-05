- 构造者模式
- 单例模式
	- 1. 类静态变量初始化实现单例模式.
	- id:: 629865a4-5176-4fe4-87f9-46a1ff1883e3
	  2. 双重锁检测方式实现单例模式
	  
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
		- 为什么uniqueInstance必须用volatile关键字修饰
		  线程安全问题，禁止指令重排序
		  ((629c5f6f-887d-462a-938b-782e6831bf57)) 
		  原因:uniqueInstance = new Singleton(); 这段代码其实是分为三步执行：
		  1. 为 uniqueInstance 分配内存空间
		  2. 初始化 uniqueInstance
		  3. 将 uniqueInstance 指向分配的内存地址.
		   
		  JVM 具有指令重排的特性，执行顺序有可能变成 1->3->2。虽然这种概率很小很小。
		  但在多线程环境下，例如，线程 T1 执行了 1 和 3，此时 T2 调用 getUniqueInstance() 后发现 uniqueInstance 不为空，因此返回 uniqueInstance，但此时 uniqueInstance 还未被初始化。
		  
		  volatile关键字的作用之一可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
-