- MVCC机制
- InnoDB锁
	- 表级锁和行级锁
	  ((62a706f1-751d-47c8-821a-eba8ed64715c))
	- [[InnoDB行级锁梳理]]
	- InnoDB 的默认隔离级别 REPEATABLE-READ（可重读）是可以解决幻读问题发生的，主要有下面两种情况：
	  1. 快照读 ：由 MVCC 机制来保证不出现幻读。
	  2. 当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读。
- [[InnoDB MVCC]]
  id:: 62ad9d9a-a979-4ad4-9323-502232fa9241
- InnoDB在可重复读事务隔离级别下解决幻读问题
  背景知识:
  快照读((62adc81c-3ded-456a-8ffb-268f72277905)) 
  当前读 ((62adc7f8-ae50-4d50-b05d-faeeb20db459)) 
  
  快照读 ：由 MVCC 机制来保证不出现幻读。
  当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读，Next-Key Lock 是行锁（Record Lock）和间隙锁（Gap Lock）的结合，行锁只能锁住已经存在的行，为了避免插入新行，需要依赖间隙锁。
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》