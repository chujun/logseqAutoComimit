- 中间件
  AP:会存在数据不一致的情况。
  eureka
  **mysql数据集群与redis集群**，由于mysql和redis的数据复制都是采用的异步复制，所以mysql数据集群与redis集群都属于AP类型，在集群中获取数据时，会存在数据不一致的情况。
  CP:
  zoomkeeper
-