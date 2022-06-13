- MVCC机制
- InnoDB锁
  InnoDB三种行级锁
  1.记录锁（Record Lock） ：也被称为记录锁，属于单个行记录上的锁。
  2. 间隙锁（Gap Lock） ：锁定一个范围，不包括记录本身。
  3. 临键锁（Next-key Lock） ：Record Lock+Gap Lock，锁定一个范围，包含记录本身。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁。
  
  InnoDB 的默认隔离级别 REPEATABLE-READ（可重读）是可以解决幻读问题发生的，主要有下面两种情况：
  1. 快照读 ：由 MVCC 机制来保证不出现幻读。
  2. 当前读 ： 使用 Next-Key Lock 进行加锁来保证不出现幻读。
-
- 资料
  [MySQL 8InnoDB 官方文档](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)
  书籍
  《MySQL 技术内幕 InnoDB 存储引擎》