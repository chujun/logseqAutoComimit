- MVCC(多版本并发控制，Multi-Version Concurrency Control)
- 当前读(current read)也叫锁定读(Locking Reads)
  如下sql操作
  ```
  select ... lock in share mode
  select ... for update
  insert、update、delete 操作
  ```
- 快照读(snapshot read)也叫一致性非锁定读(Consistent Nonlocking Reads)
- RC 这种隔离级别中，还支持"半一致读"
  在 RC 中，只会对索引增加Record Lock，不会添加Gap Lock和Next-Key Lock。
  在 RR 中，为了解决幻读的问题，在支持Record Lock的同时，还支持Gap Lock和Next-Key Lock；
  [MySQL 默认隔离级别是RR，为什么阿里等大厂会改成RC？](https://mp.weixin.qq.com/s/mIz0T0v68_dvUgCrj-qdug)