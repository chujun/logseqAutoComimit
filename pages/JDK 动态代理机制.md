- 从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中。
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
  
  具体代码实例就不介绍了，比较简单
- 进一步深入研究代理
  ProxyClassFactory
  根据 ProxyGenerator.generateProxyClass(proxyClassName, interfaces);生成代理类的二进制字节流
- 如何查看代理类生成的类字节码内容
- 具体实战演练
  ```java
  @UtilityClass
  public class ProxyClassUtils {
      /**
       * 可以查看代理类生成的内容
       */
      public static void generateProxyClass(String path, String proxyClassName, Class<?>[] interfaces) {
          byte[] bytes = ProxyGenerator.generateProxyClass(proxyClassName, interfaces);
          try {
            	//import org.apache.commons.io.FileUtils;
              FileUtils.writeByteArrayToFile(new File(path + "/" + proxyClassName + ".class"), bytes);
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
  
      }
  }
  ```
  单元测试
  ```java
  ProxyClassUtils.generateProxyClass("/Users/chujun/my/project/javaddu/target/proxy", "Calculator$proxy0", new Class[]{Calculator.class});
  ```
  生成的动态代理类反编译之后内容如下
  ```class
  
  
  import com.jun.chu.java.proxy.aop.service.Calculator;
  import java.lang.reflect.InvocationHandler;
  import java.lang.reflect.Method;
  import java.lang.reflect.Proxy;
  import java.lang.reflect.UndeclaredThrowableException;
  
  public final class Calculator$proxy0 extends Proxy implements Calculator {
      private static Method m1;
      private static Method m3;
      private static Method m2;
      private static Method m4;
      private static Method m0;
  
      public Calculator$proxy0(InvocationHandler var1) throws  {
          super(var1);
      }
  
      public final boolean equals(Object var1) throws  {
          try {
              return (Boolean)super.h.invoke(this, m1, new Object[]{var1});
          } catch (RuntimeException | Error var3) {
              throw var3;
          } catch (Throwable var4) {
              throw new UndeclaredThrowableException(var4);
          }
      }
  
      public final int calculate(int var1, int var2) throws  {
          try {
              return (Integer)super.h.invoke(this, m3, new Object[]{var1, var2});
          } catch (RuntimeException | Error var4) {
              throw var4;
          } catch (Throwable var5) {
              throw new UndeclaredThrowableException(var5);
          }
      }
  
      public final String toString() throws  {
          try {
              return (String)super.h.invoke(this, m2, (Object[])null);
          } catch (RuntimeException | Error var2) {
              throw var2;
          } catch (Throwable var3) {
              throw new UndeclaredThrowableException(var3);
          }
      }
  
      public final int sum(int var1, int var2) throws  {
          try {
              return (Integer)super.h.invoke(this, m4, new Object[]{var1, var2});
          } catch (RuntimeException | Error var4) {
              throw var4;
          } catch (Throwable var5) {
              throw new UndeclaredThrowableException(var5);
          }
      }
  
      public final int hashCode() throws  {
          try {
              return (Integer)super.h.invoke(this, m0, (Object[])null);
          } catch (RuntimeException | Error var2) {
              throw var2;
          } catch (Throwable var3) {
              throw new UndeclaredThrowableException(var3);
          }
      }
  
      static {
          try {
              m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
              m3 = Class.forName("com.jun.chu.java.proxy.aop.service.Calculator").getMethod("calculate", Integer.TYPE, Integer.TYPE);
              m2 = Class.forName("java.lang.Object").getMethod("toString");
              m4 = Class.forName("com.jun.chu.java.proxy.aop.service.Calculator").getMethod("sum", Integer.TYPE, Integer.TYPE);
              m0 = Class.forName("java.lang.Object").getMethod("hashCode");
          } catch (NoSuchMethodException var2) {
              throw new NoSuchMethodError(var2.getMessage());
          } catch (ClassNotFoundException var3) {
              throw new NoClassDefFoundError(var3.getMessage());
          }
      }
  }
  
  ```
-