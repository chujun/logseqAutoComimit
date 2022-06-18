- MVCC机制
- InnoDB锁
	- 表级锁和行级锁
	  ((62a706f1-751d-47c8-821a-eba8ed64715c))
	- [[InnoDB行级锁梳理]]
	- InnoDB 的默认隔离级别 REPEATABLE-READ（可重读）是可以解决幻读问题发生的，主要有下面两种情况：
	  1. 快照读 ：由 MVCC 机制来保证不出现幻读。
	  2. 当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读。
- MVCC机制
  id:: 62ad9d9a-a979-4ad4-9323-502232fa9241
  MVCC 的实现依赖于：隐藏字段、Read View、undo log。在内部实现中，InnoDB 通过数据行的 DB_TRX_ID 和 Read View 来判断数据的可见性，如不可见，则通过数据行的 DB_ROLL_PTR 找到 undo log 中的历史版本。每个事务读到的数据版本可能是不一样的，在同一个事务中，用户只能看到该事务创建 Read View 之前已经提交的修改和该事务本身做的修改。
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》