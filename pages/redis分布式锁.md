- 需要考虑的问题
  分布式集群
  
  锁超时
  服务挂了导致没有及时释放锁
  
  锁释放：
  A线程上锁,但是其他线程释放锁
- 资料
  [Distributed Locks with Redis 官方资料](https://redis.io/docs/reference/patterns/distributed-locks/)