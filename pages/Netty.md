- 是一个高性能、异步事件驱动的 NIO 框架，基于NIO。
  Netty 比 JDK NIO 做的更多，解决了NIO的很多潜在bug。
  比如解决了粘包半包、断连和 idle 处理、支持流量整形等。
- 为什么基于NIO，而不基于AIO？
  有一个基于AIO实现了，但是性能提升不明显,又回退了NIO。
- Netty 的 Reactor 模型