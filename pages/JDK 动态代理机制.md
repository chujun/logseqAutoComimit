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
  第一个参数指明使用哪个类加载器生成类字节码.
  第二个参数指明代理类