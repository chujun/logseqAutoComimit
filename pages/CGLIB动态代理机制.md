- CGLIB(Code Generation Library)是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。
  CGLIB 通过继承方式实现代理。
- 开源框架都使用到了CGLIB， 
  例如 Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理。
- [[JDK动态代理机制]]和 [[CGLIB动态代理机制 ]]对比
  1. JDK 动态代理只能代理实现了接口的类或者直接代理接口，而CGLIB可以代理未实现任何接口的类。
  2. CGLIB 动态代理是通过生成一个被代理类的子类来拦截被代理类的方法调用，因此不能代理声明为 final 类型的类和方法。(只能调用被代理对象的原始方法)
  3. 效率比较
  大部分情况都是 JDK 动态代理更优秀，随着 JDK 版本的升级，这个优势更加明显。
- 核心类介绍
  MethodInterceptor 接口和 Enhancer 类是核心
  ```java
  public interface MethodInterceptor
  extends Callback{
      // 拦截被代理类中的方法
      public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,
                                 MethodProxy proxy) throws Throwable;
  }
  ```
  obj :被代理的对象（需要增强的对象）
  method :被拦截的方法（需要增强的方法）
  args :方法入参
  proxy :用于调用原始方法
  
  Enhancer类来动态创建被代理类，
  当代理类调用方法的时候，实际调用的是 MethodInterceptor 中的 intercept 方法。
  ```java
  import net.sf.cglib.proxy.Enhancer;
  
  public class CglibProxyFactory {
  
      public static Object getProxy(Class<?> clazz,MethodInterceptor methodInterceptor) {
          // 创建动态代理增强类
          Enhancer enhancer = new Enhancer();
          // 设置类加载器
          enhancer.setClassLoader(clazz.getClassLoader());
          // 设置被代理类
          enhancer.setSuperclass(clazz);
          // 设置方法拦截器
          enhancer.setCallback(methodInterceptor);
          // 创建代理类
          return enhancer.create();
      }
  }
  ```
- 实操创建一个动态代理类举例
  0.导入jar包
  ```xml
  <dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
  </dependency>
  ```
  1.被代理的类
  ```java
  public class AliSmsService {
      public String send(String message) {
          System.out.println("send message:" + message);
          return message;
      }
  
      public final String sendFinal(String message) {
          System.out.println("sendV2 message:" + message);
          return message;
      }
  }
  ```
  2.利用Enhancer创建动态代理类
  ```java
  public class CglibProxyFactory {
      public static <T> T getProxy(Class<T> clazz, MethodInterceptor methodInterceptor) {
          // 创建动态代理增强类
          Enhancer enhancer = new Enhancer();
          // 设置类加载器
          enhancer.setClassLoader(clazz.getClassLoader());
          // 设置被代理类
          enhancer.setSuperclass(clazz);
          // 设置方法拦截器
          enhancer.setCallback(methodInterceptor);
          // 创建代理类
          return (T) enhancer.create();
      }
  }
  ```
  
  3.创建一个类实现一个MethodInterceptor
  ```java
  public class DebugMethodInterceptor implements MethodInterceptor {
      @Override
      public Object intercept(final Object obj, final Method method, final Object[] args, final MethodProxy proxy) throws Throwable {
          //调用方法之前，我们可以添加自己的操作
          System.out.println("before method: " + method.getName());
          //调用被代理对象的原始方法
          Object object = proxy.invokeSuper(obj, args);
          //调用方法之后，我们同样可以添加自己的操作
          System.out.println("after method: " + method.getName());
          return object;
      }
  }
  ```
  10 单元自测
  ```java
  public class CglibProxyTest {
      @Test
      public void test() {
          AliSmsService aliSmsService = CglibProxyFactory.getProxy(AliSmsService.class,
              new DebugMethodInterceptor());
          aliSmsService.send("java");
          //before method send
          //send message:java
          //after method send
      }
  
      @Test
      public void testSendFinal() {
          AliSmsService aliSmsService = CglibProxyFactory.getProxy(AliSmsService.class,
              new DebugMethodInterceptor());
          aliSmsService.sendFinal("java");
          //sendV2 message:java
      }
  
  }
  ```
-
-