- MVCC机制
- InnoDB锁
	- 表级锁和行级锁
	  ((62a706f1-751d-47c8-821a-eba8ed64715c))
	- [[InnoDB行级锁]]
	- InnoDB 的默认隔离级别 REPEATABLE-READ（可重读）是可以解决幻读问题发生的，主要有下面两种情况：
	  1. 快照读 ：由 MVCC 机制来保证不出现幻读。
	  2. 当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读。
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》