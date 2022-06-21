- redis作为内存数据库，支持将数据持久化到磁盘。
  主要有两种持久化机制
- RDB持久化(快照 snapshotting)
  方案：通过创建快照来获得存储在内存里面的数据在某个时间点上的副本
  快照持久化是 Redis 默认采用的持久化方式，在 redis.conf 配置文件中默认有此下配置：
  ```
  save 900 1           #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  
  save 300 10          #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  
  save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  ```
  RDB 文件保存位置都是通过 dir 参数设置的，默认的文件名是 dump.rdb。
- AOF持久化(只追加文件，append only file)
  方案：开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到内存缓存 server.aof_buf 中，然后再根据 appendfsync 配置来决定何时将其同步到硬盘中的 AOF 文件。
  与快照持久化相比
  AOF 持久化的实时性更好，因此已成为主流的持久化方案。
  默认情况下 Redis 没有开启 AOF（append only file）方式的持久化，可以通过 appendonly 参数开启：
  ```
  appendonly yes
  ```
  AOF 文件的保存位置和 RDB 文件的位置相同，都是通过 dir 参数设置的，默认的文件名是 appendonly.aof。
-