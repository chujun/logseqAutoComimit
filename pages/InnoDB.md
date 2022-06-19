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
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》