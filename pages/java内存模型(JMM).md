- 基本要求
  1. 跨平台.屏蔽各种硬件和操作系统内存访问差异，让java程序在各个平台和硬件上达到一直的访问效果。
  2. 足够灵活:这样可以让jvm有足够的发挥空间利用底层硬件的各种特性(寄存器,高速缓存和指令集中的某些特有指令)提高执行性能。
- Java内存模型主要内容
  Java内存模型的主要目的是定义程序中各种变量的访问规则，即关注在虚拟机中把变量值存储到内存和从内存中取出变量值这样的底层细节。
- 主内存和工作内存
  
  主内存:所有的变量都存储在主内存（Main Memory）中（与物理硬件内存类比）
  工作内存:每条线程还有自己的工作内存（Working Memory，可处理器高速缓存类比）,线程的工作内存中保存了被该线程使用的变量的主内存副本。
  
  线程与工作内存，主内存的关系
  1. 每个线程只能访问自己的工作内存，无法访问其他线程的工作内存，也不能直接读取主内存数据。
  2. 线程间变量值的传递均需要通过主内存来完成
  
  主内存和工作内存提到的变量理解
  此处的变量（Variables）与Java编程中所说的变量有所区别，它包括了实例字段、静态字段和构成数组对象的元素，但是不包括局部变量与方法参数，因为后者是线程私有的，不会被共享，自然就不会存在竞争问题。
  
  ![截屏2022-06-05 上午10.36.45.png](../assets/截屏2022-06-05_上午10.36.45_1654396690930_0.png){:height 335, :width 798}
  ![image.png](../assets/image_1654243989141_0.png)
- 主内存和工作内存交互协议
  id:: 6299c26b-666a-4ac1-9ec4-0ddf6b9b2033
	- 定义:主内存与工作内存之间具体的交互协议，即一个变量如何从主内存拷贝到工作内存、如何从工作内存同步回主内存这一类的实现细节。
	- 说明:
	  这个协议比较复杂,不过也无须过分担忧，除了进行虚拟机开发的团队外，大概没有其他开发人员会以这种方式来思考并发问题，只需要理解Java内存模型的定义即可
	  --->会有一个等效判断原则——先行发生原则，用来确定一个操作在并发环境下是否安全的。
	- 虚拟机保证如下8种操作必定是原子性的。
	  原子性操作
	  （对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许有例外）
	  
	  ·lock（锁定）：作用于主内存的变量，它把一个变量标识为一条线程独占的状态。
	  ·unlock（解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
	  ·read（读取）：作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用。
	  ·load（载入）：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。
	  ·use（使用）：作用于工作内存的变量，它把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用变量的值的字节码指令时将会执行这个操作。
	  ·assign（赋值）：作用于工作内存的变量，它把一个从执行引擎接收的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。
	  ·store（存储）：作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后的write操作使用。
	  ·write（写入）：作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。
	- 变量操作顺序
	  如果要把一个变量从主内存拷贝到工作内存，那就要按顺序执行read和load操作，
	  如果要把变量从工作内存同步回主内存，就要按顺序执行store和write操作。
	  ![主内存工作内存变量主要操作示意图.png](../assets/主内存工作内存变量主要操作示意图_1654398769480_0.png) 
	  
	  注意:顺序执行而非连续执行
	  也就是说read与load之间、store与write之间是可插入其他指令的，如对主内存中的变量a、b进行访问时，一种可能出现的顺序是read a、read b、load b、load a。
	- Java内存模型还规定了在执行上述8种基本操作时必须满足如下规则:
	  ·不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内存不接受，或者工作内存发起回写了但主内存不接受的情况出现。
	  ·不允许一个线程丢弃它最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回主内存。
	  ·不允许一个线程无原因地（没有发生过任何assign操作）把数据从线程的工作内存同步回主内存中。
	  ·一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量，换句话说就是对一个变量实施use、store操作之前，必须先执行assign和load操作。
	  ·一个变量在同一个时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。
	  ·如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作以初始化变量的值。
	  ·如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定的变量。
	  ·对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）。
	  后来Java设计团队将Java内存模型的操作描述简化为read、write、lock和unlock四种，但这只是语言描述上的等价化简，Java内存模型的基础设计并未改变.
	- Java内存模型专门针对volatile字段的一些特殊规定
	  id:: 629c66c3-c611-4878-b8e5-2227178472f0
	  
	  假定T表示一个线程，V和W分别表示两个volatile型变量，那么在进行read、load、use、assign、store和write操作时需要满足如下规则
	  1·只有当线程T对变量V执行的前一个动作是load的时候，线程T才能对变量V执行use动作；并且，只有当线程T对变量V执行的后一个动作是use的时候，线程T才能对变量V执行load动作。线程T对变量V的use动作可以认为是和线程T对变量V的load、read动作相关联的，必须连续且一起出现。
	  规则说明:这条规则要求在工作内存中，每次使用V前都必须先从主内存刷新最新的值，用于保证能看见其他线程对变量V所做的修改。
	  read,load,use连续并且一起出现，缺一不可
	  
	  2·只有当线程T对变量V执行的前一个动作是assign的时候，线程T才能对变量V执行store动作；并且，只有当线程T对变量V执行的后一个动作是store的时候，线程T才能对变量V执行assign动作。线程T对变量V的assign动作可以认为是和线程T对变量V的store、write动作相关联的，必须连续且一起出现。
	  规则说明:这条规则要求在工作内存中，每次修改V后都必须立刻同步回主内存中，用于保证其他线程可以看到自己对变量V所做的修改。
	  assign,store,write连续并且一起出现，缺一不可
	  
	  3·假定动作A是线程T对变量V实施的use或assign动作，假定动作F是和动作A相关联的load或store动作，假定动作P是和动作F相应的对变量V的read或write动作；与此类似，假定动作B是线程T对变量W实施的use或assign动作，假定动作G是和动作B相关联的load或store动作，假定动作Q是和动作G相应的对变量W的read或write动作。如果A先于B，那么P先于Q。
	  规则说明:这条规则要求volatile修饰的变量不会被指令重排序优化，从而保证代码的执行顺序与程序的顺序相同
	  
	  
	  ((629c2156-6c97-400d-865b-80a8f71be9b0))
- jvm对于volatile型变量的特殊规则说明
  id:: 629c2156-6c97-400d-865b-80a8f71be9b0
  volatile型变量 jvm保证两个特性:内存可见性和指令禁止重排序性
	- 深入理解知识
	  具体特殊规则是什么，不了解也不影响后续理解
	  ((629c66c3-c611-4878-b8e5-2227178472f0))
	- volatile内存可见性
	  定义:当一条线程修改了这个volatile变量的值，新值对于其他线程来说是可以立即得知的.
	  
	  普通变量
	  普通变量并不能做到这一点，普通变量的值在线程间传递时均需要通过主内存来完成。
	  比如，线程A修改一个普通变量的值，然后向主内存进行回写，另外一条线程B在线程A回写完成了之后再对主内存进行读取操作，新变量值才会对线程B可见。
	- volatile禁止指令重排序性
	  id:: 629c5f6f-887d-462a-938b-782e6831bf57
	  
	  普通变量
	  普通的变量仅会保证在该方法的执行过程中所有依赖赋值结果的地方都能获取到正确的结果，而不能保证变量赋值操作的顺序与程序代码中的执行顺序一致。因为在同一个线程的方法执行过程中无法感知到这点，这就是Java内存模型中描述的所谓“线程内表现为串行的语义”（Within-Thread As-If-Serial Semantics）。
	  
	  实现原理:
	  内存屏障（Memory Barrier或Memory Fence）:指重排序时不能把后面的指令重排序到内存屏障之前的位置。
	  
	  来看一个指令重排序可能导致问题的例子。
	  例子1:双重锁检测方式实现单例模式
	  ((629865a4-5176-4fe4-87f9-46a1ff1883e3)) 
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
	  可以观察加入volatile和未加入volatile关键字时所生成的汇编代码的差别（如何获得即时编译的汇编代码？请参考第4章关于HSDIS插件的介绍）。
	  ![截屏2022-06-05 下午3.54.20.png](../assets/截屏2022-06-05_下午3.54.20_1654415703081_0.png) 
	  对比变化，关键变化在于有volatile修饰的变量，赋值后（前面mov%eax，0x150(%esi)这句便是赋值操作）多执行了一个“lock addl$0x0，(%esp)”操作。
	  
	  这个操作的作用相当于一个内存屏障（Memory Barrier或Memory Fence），指重排序时不能把后面的指令重排序到内存屏障之前的位置。
	  
	  >这句指令中的“addl$0x0，(%esp)”（把ESP寄存器的值加0）显然是一个空操作，之所以用这个空操作而不是空操作专用指令nop，是因为IA32手册规定lock前缀不允许配合nop指令使用。这里的关键在于lock前缀，查询IA32手册可知，它的作用是将本处理器的缓存写入了内存，该写入动作也会引起别的处理器或者别的内核无效化（Invalidate）其缓存，这种操作相当于对缓存中的变量做了一次前面介绍Java内存模式中所说的“store和write”操作[插图]。所以通过这样一个空操作，可让前面volatile变量的修改对其他处理器立即可见。---深入java虚拟机:JVM高级特性与最佳实践-12.3java内存模型
	  
	  
	  例子2:开发过程中常见配置读取过程的伪代码例子
	  我们在处理配置文件时一般不会出现并发，所以没有察觉这儿可能会有问题。
	  如果initialized不用volatile修饰,就可能由于指令重排序,导致"initialized = true;"被置于配置初始化工作代码之前先执行,因为这个执行顺序对线程init方法是不影响的。这就导致线程B可能执行错误了。
	  ```java
	  public class VolatileInstructionResort {
	      private Map<String, String> conf;
	      //此变量必须用volatile,会有指令重排序问题，这儿内存可见性问题反倒不是问题,大不了多sleep几次。
	      volatile boolean initialized = false;
	      //线程A初始化
	      private void init() {
	          conf = new HashMap<>();
	          //进行其他初始化配置
	          processInitConfig(conf);
	          //initialized状态变量设置为true,告诉其他线程配置可用了
	          initialized = true;
	      }
	  
	      private void processInitConfig(final Map<String, String> conf) {
	  		//do config init work
	      }
	  	
	  	//线程B执行
	      private void execute() throws InterruptedException {
	          //等待配置初始化完成，在进行后续其他操作
	          while (!initialized) {
	              Thread.sleep(100);
	          }
	  
	          doSomethingWithConf();
	      }
	  
	      /**
	       * 方法约定:配置完成后才能掉该方法，不然会抛出异常
	       */
	      private void doSomethingWithConf() {
	  		
	      }
	  }
	  ```
	- 错误认知:基于volatile变量的运算在并发下是线程安全的
	  锁的原子性要求并没有满足 ((6299c682-4f78-4d5f-9d9c-2e032d7d8e8f)) 
	  因为Java里面的运算操作符存在非原子操作的运算(例如a++自增运算)，这导致volatile变量的运算在并发下一样是不安全的，
	  举例:
	  ```java
	  public class VolatileTest {
	      private static volatile int count = 0;
	      private static final int THREAD_COUNT = 20;
	  
	      private static void increase() {
	          count++;
	      }
	  
	      @Test
	      public void test() throws InterruptedException {
	          Thread[] threads = new Thread[THREAD_COUNT];
	          for (int i = 0; i < threads.length; i++) {
	              threads[i] = new Thread(() -> {
	                  for (int j = 0; j < 10000; j++) {
	                      increase();
	                  }
	              });
	              threads[i].start();
	          }
	          for (int i = 0; i < threads.length; i++) {
	              threads[i].join();
	          }
	          //每次输出结果都不一样，小于10000*20=200000
	          System.out.println(count);
	      }
	  }
	  ```
	  运行结果:
	  最后输出的结果应该是200000。然而运行完这段代码之后，并不会获得期望的结果，而且会发现每次运行程序，输出的结果都不一样，都是一个小于200000的数字
	  
	  javap反编译后jvm字节码
	  ![截屏2022-06-05 下午12.05.53.png](../assets/截屏2022-06-05_下午12.05.53_1654402006752_0.png)
	  
	  并发更新失败原因:
	  当getstatic指令把race的值取到操作栈顶时，volatile关键字保证了race的值在此时是正确的，但是在执行iconst_1、iadd这些指令的时候，其他线程可能已经把race的值改变了，而操作栈顶的值就变成了过期的数据，所以putstatic指令执行后就可能把较小的race值同步回主内存之中。
	  
	  需要说明的一点:哪怕是一条jvm指令码也不意味着一定就是原子性操作.
	  一条字节码指令在解释执行时，解释器要运行许多行代码才能实现它的语义。
	  如果是编译执行，一条字节码指令也可能转化成若干条本地机器码指令.
	  此处使用-XX：+PrintAssembly参数输出反汇编来分析才会更加严谨一些，但是考虑到读者阅读的方便性，并且字节码已经能很好地说明问题，所以此处使用字节码来解释。
	- 单独使用volatile字段变量线程安全的使用场景
	  1. 运算结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。
	  2. 变量不需要与其他的状态变量共同参与不变约束。
	  举例：
	  ```java
	  public class VolatileThreadSafe {
	      //状态变量,0表示初始化，+1表示启动，-1表示停止，boolean shutdown当然也可以
	      private volatile int ctl;
	  
	      private void shutdown() {
	          ctl = -1;
	      }
	  
	      private void start() {
	          ctl = 1;
	      }
	  
	      private void execute() {
	          while (ctl > 0) {
	              //dowork
	          }
	      }
	  }
	  ```
	  这类场景中就很适合使用volatile变量来控制并发，当shutdown()方法被调用时，能保证所有线程中执行的doWork()方法都立即停下。
	  
	  反例:
	  而ReentrantLock.Sync的nonfairTryAcquire,state==0判断则算是依赖了stata的当前值,所以只能用volititle+CAS机制实现无锁化加锁方案
	  线程A，B可能同时停留在"int c=getSate()"这行
	  ```java
	  private volatile int state;
	  final boolean nonfairTryAcquire(int acquires) {
	              final Thread current = Thread.currentThread();
	              int c = getState();
	              if (c == 0) {
	                  if (compareAndSetState(0, acquires)) {
	                      setExclusiveOwnerThread(current);
	                      return true;
	                  }
	              }else if (current == getExclusiveOwnerThread()) {
	                  int nextc = c + acquires;
	                  if (nextc < 0) // overflow
	                      throw new Error("Maximum lock count exceeded");
	                  setState(nextc);
	                  return true;
	              }
	              return false;
	  }
	  ```
	- volatitle变量与普通变量和锁性能比较
	  volatitle变量与普通变量:volatile变量读操作的性能消耗与普通变量几乎没有什么差别，但是写操作则可能会慢上一些，因为它需要在本地代码中插入许多内存屏障指令来保证处理器不发生乱序执行
	  volatitle变量与锁比较:大多数场景下volatile的总开销仍然要比锁来得更低。
	  因此我们在volatile与锁中选择的唯一判断依据仅仅是volatile的语义能否满足使用场景的需求。
	-
- 先行发生原则
  作用:可以用来确定一个操作在并发环境下是否安全的。
- 资料
  《深入理解java虚拟机：JVM高级特性与最佳实践》第三版 12.3java内存模型与线程-Java内存模型