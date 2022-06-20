- 需要考虑的问题
  分布式集群
  原子性:Lua脚本，或者扩展的SET NX EX命令
  
  服务挂了导致锁无法释放问题--->引入锁超时机制
  
  引入锁超时机制，引入另一个问题:在加锁和释放锁之间的逻辑执行得太长，以至于超出了锁的超时限制--->
  解决这个办法：
  1. Redis 分布式锁不要用于较长时间的任务
  2. 
  
  锁释放：
  A线程上锁,但是其他线程释放锁
- 资料
  [Distributed Locks with Redis 官方资料](https://redis.io/docs/reference/patterns/distributed-locks/)