- CGLIB(Code Generation Library)是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。
  CGLIB 通过继承方式实现代理。
- 开源框架都使用到了CGLIB， 
  例如 Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理。
- JDK动态代理和 CGLIB动态代理对比
  1. JDK 动态代理只能代理实现了接口的类或者直接代理接口，而CGLIB可以代理未实现任何接口的类。