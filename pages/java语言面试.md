- 基础
  0. java规范
  [Java SE Specifications java规范官方文档链接](https://docs.oracle.com/javase/specs/index.html)
  [java语言规范8官方文档链接](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)
  [jvm规范8官方文档链接](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)
  
  1. java语言特点
  面向对象三大特性:封装，继承，多态
  2. jdk vs jre
  3. OpenJDK vs OracleJDK
  [openjdk github](https://github.com/openjdk/jdk)
  10.语言
  注解
  序列化/反序列化
  101. 重写
  重写: 方法名、参数列表必须相同，子类方法返回值类型应比父类方法返回值类型更小或相等，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。
  理解：继承父类方法重写,子类方法的申明必须与父类保持兼容,不然就不满足设计原则之里氏替换原则了。
  102. 包装类型的缓存机制了解么？
  Integer:[-128,127]
  103. 深拷贝和浅拷贝区别
  [深拷贝和浅拷贝区别](https://javaguide.cn/java/basis/java-basic-questions-02.html#%E6%B7%B1%E6%8B%B7%E8%B4%9D%E5%92%8C%E6%B5%85%E6%8B%B7%E8%B4%9D%E5%8C%BA%E5%88%AB%E4%BA%86%E8%A7%A3%E5%90%97-%E4%BB%80%E4%B9%88%E6%98%AF%E5%BC%95%E7%94%A8%E6%8B%B7%E8%B4%9D)
  104. 字符串常量池
  105. String的intern方法作用
  ```java
  // 在堆中创建字符串对象”Java“
  // 将字符串对象”Java“的引用保存在字符串常量池中
  String s1 = "Java";
  // 直接返回字符串常量池中字符串对象”Java“对应的引用
  String s2 = s1.intern();
  // 会在堆中在单独创建一个字符串对象
  String s3 = new String("Java");
  // 直接返回字符串常量池中字符串对象”Java“对应的引用
  String s4 = s3.intern();
  // s1 和 s2 指向的是堆中的同一个对象
  System.out.println(s1 == s2); // true
  // s3 和 s4 指向的是堆中不同的对象
  System.out.println(s3 == s4); // false
  // s1 和 s4 指向的是堆中的同一个对象
  System.out.println(s1 == s4); //true
  ```
  106. 字符串"+"拼接引发的
  编译期常量折叠代码优化
  ```java
  String str1 = "str";
  String str2 = "ing";
  String str3 = "str" + "ing";
  String str4 = str1 + str2;
  String str5 = "string";
  System.out.println(str3 == str4);//false
  System.out.println(str3 == str5);//true
  System.out.println(str4 == str5);//false
  ```
  1. java方法调用形参是值传递还是引用传递? 
  方法实参和形参
  明确值传递和引用传递定义。
  值传递:方法接收的是实参值的拷贝。
  引用传递:方法接收的直接是实参所引用的对象在堆中的地址，不会创建副本，对形参的修改将影响到实参。
  引用类型形参存储的值是对象的引用地址。
  无论是基本类型和是引用类型，在实参传入形参时，都是值传递。Java 中只有值传递，
  C语言有值传递和引用传递两种方式
  [Java 中只有值传递](https://github.com/Snailclimb/JavaGuide/blob/main/docs/java/basis/why-there-only-value-passing-in-java.md)
  [这篇介绍更详细清晰,如果还是比较迷惑的话](https://segmentfault.com/a/1190000016773324)
- 集合
- 泛型
- 反射
  JDK实现动态代理
  InvocationHandler,
  java Proxy创建代理类，必须指明类加载器,需要实现的接口
  ```java
  public static Object getProxy(Object targetBusinessObject, AbstractBaseHandler handler) {
          Object proxyObject = targetBusinessObject;
          handler.setTargetBusinessObject(targetBusinessObject);
  
          proxyObject = Proxy.newProxyInstance(targetBusinessObject.getClass().getClassLoader(), targetBusinessObject.getClass().getInterfaces(), handler);
  
          return proxyObject;
      }
  ```
- 并发
- JVM
- 新特性