- 从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的
- **Proxy** , **InvocationHandler** 类是核心
  核心:使用Proxy类newProxyInstance()方法生成代理对象
  ```java
  
      public static Object newProxyInstance(ClassLoader loader,
                                            Class<?>[] interfaces,
                                            InvocationHandler h)
          throws IllegalArgumentException
      {
  		...
  	}
  ```
  第一个参数指明使用哪个类加载器生成类字节码。
  第二个参数指明代理类实现了哪些接口。
  第三个参数指明客户端调用代理类的代理方法时，代理类该怎么处理。
  
  而InvocationHandler就指明了代理类该怎么处理代理方法
  ```java
  public interface InvocationHandler {
  
      /**
       * 当你使用代理对象调用方法的时候实际会调用到这个方法
       */
      public Object invoke(Object proxy, Method method, Object[] args)
          throws Throwable;
  }
  ```
  第一个参数proxy :动态生成的代理类
  第二个参数method : 与代理类对象调用的方法相对应,代理类接口声明的方法
  第三个参数args : 当前 method 方法的参数
-