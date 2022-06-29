- 中间件
  AP:会存在数据不一致的情况。
  集群数据复制采用异步复制方式的一般属于AP类型,保证高可用性,但是不保证数据强一致性。
  eureka，kafka ，RocketMQ的NameServer
  **mysql数据集群与redis集群**，由于mysql和redis的数据复制都是采用的异步复制，所以mysql数据集群与redis集群都属于AP类型，在集群中获取数据时，会存在数据不一致的情况。
  CP:实现了一致性算法的中间件(Paxos,Raft)
  zookeeper
-