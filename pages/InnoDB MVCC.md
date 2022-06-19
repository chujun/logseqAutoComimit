- MVCC(多版本并发控制，Multi-Version Concurrency Control)
- 当前读(current read)也叫锁定读(Locking Reads)
  如下sql操作
  ```
  select ... lock in share mode
  select ... for update
  insert、update、delete 操作
  ```
  锁定读会对读取到的记录加锁：
  select ... lock in share mode：对记录加 S 锁，其它事务也可以加S锁，如果加 x 锁则会被阻塞。
  select ... for update、insert、update、delete：对记录加 X 锁，且其它事务不能加任何锁。
- 快照读(snapshot read)也叫一致性非锁定读(Consistent Nonlocking Reads)
  sql操作
  ```
  普通的select语句
  ```
  常规实现思路
  记录版本:指的是表数据行记录的版本
  通常做法是加一个版本号或者时间戳字段，在更新数据的同时版本号 + 1 或者更新时间戳。查询时，将当前可见的版本号与对应记录的版本号进行比对，如果记录的版本小于可见版本，则表示该记录可见.
  
  InnoDB实现机制:MVCC
  如果读取的行正在执行 DELETE 或 UPDATE 操作，这时读取操作不会去等待行上锁的释放。相反地，InnoDB 存储引擎会去读取行的一个快照数据。
- 事务隔离级别和快照读，当前读的关系
  在 Repeatable Read 和 Read Committed 两个隔离级别下，如果是执行普通的 select 语句（不包括 select ... lock in share mode ,select ... for update）则会使用 一致性非锁定读（MVCC）。
  并且在 Repeatable Read 下 MVCC 实现了可重复读和防止部分幻读.
- RC 这种隔离级别中，还支持"半一致读"
  在 RC 中，只会对索引增加Record Lock，不会添加Gap Lock和Next-Key Lock。
  在 RR 中，为了解决幻读的问题，在支持Record Lock的同时，还支持Gap Lock和Next-Key Lock；
  [MySQL 默认隔离级别是RR，为什么阿里等大厂会改成RC？](https://mp.weixin.qq.com/s/mIz0T0v68_dvUgCrj-qdug)