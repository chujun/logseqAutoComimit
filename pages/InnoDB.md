- MVCC机制
- InnoDB锁
	- 表级锁和行级锁
	  ((62a706f1-751d-47c8-821a-eba8ed64715c))
	- [[InnoDB行级锁梳理]]
- [[InnoDB MVCC]]
  id:: 62ad9d9a-a979-4ad4-9323-502232fa9241
- InnoDB在可重复读事务隔离级别下解决幻读问题
  标准SQL的可重复读事务隔离级别下是不能解决幻读问题的，但是InnoDB解决了幻读问题
  背景知识:
  快照读 ((62ae83ab-0d03-4ba3-83a5-65b55e03f2ae)) 
  当前读 ((62adc7f8-ae50-4d50-b05d-faeeb20db459)) 
  临键锁 ((62a9a234-26ad-4d55-a02b-9813368de3a4)) 
  
  1. 快照读：由 MVCC 机制来保证不出现幻读。
  RR 隔离级别只会在事务开启后的第一次查询生成 Read View ，并使用至事务提交。
  所以在生成 Read View 之后其它事务所做的更新、插入记录版本对当前事务并不可见，实现了可重复读和防止快照读下的 “幻读”
  2. 当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读，Next-Key Lock 是行锁（Record Lock）和间隙锁（Gap Lock）的结合，行锁只能锁住已经存在的行，为了避免插入新行，需要依赖间隙锁。
  每次读取的都是最新数据，这时如果两次查询中间有其它事务插入数据，就会产生幻读。
  InnoDB 在实现Repeatable Read 时，如果执行的是当前读，则会对读取的记录使用 Next-key Lock ，来防止其它事务在间隙间插入数据
- 为什么MySQL选择REPEATABLE READ作为默认隔离级别？
  MySQL在主从复制的过程是通过bin log 进行数据同步的。
  MySQL早期只有statement这种bin log格式，而这种格式记录的是原始sql，可能导致主从数据不一致问题。
  *当并行复制乱序提交引起的同步异常的时候，就会导致备库在 SQL 回放之后，结果和主库内容不一致。*
  为了解决这个问题，MySQL默认采用了Repetable Read这种隔离级别
- MySQL 默认隔离级别是RR，为什么阿里等大厂会改成RC？
  互联网公司和传统企业最大的区别是什么？高并发
  原因
  1. 提升并发：RC 比 RR 的并发度要好
  a。RC 在加锁的过程中，是不需要添加Gap Lock和 Next-Key Lock 的，只对要修改的记录添加行级锁就行了。使得并发度要比 RR 高很多。
  b。RC 还支持"半一致读"，可以大大的减少了更新语句时行锁的冲突；对于不满足更新条件的记录，可以提前释放锁，提升并发度。
  ((62aecb9c-c423-4636-ad3a-07acad0a46bb)) 
  2. 减少死锁
  RR这种事务隔离级别会增加Gap Lock和 Next-Key Lock，这就使得锁的粒度变大，那么就会使得死锁的概率增大。
  3。幻读问题
  虽然RC相对RR存在幻读问题，但是这个其实还好，很多时候幻读问题其实是可以忽略的，或者可以用其他手段解决。
  所有的技术方案的选择，都是一种权衡的艺术！
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》
  博客
  [MySQL 默认隔离级别是RR，为什么阿里等大厂会改成RC？](https://mp.weixin.qq.com/s/mIz0T0v68_dvUgCrj-qdug)