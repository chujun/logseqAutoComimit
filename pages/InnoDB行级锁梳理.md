- 行锁触发条件是查询条件命中索引，MySQL默认使用临键锁
  需要说明的是上下文提到的”查询条件命中索引“，并不意味着有符合条件的记录。
- 记录锁(Record Lock)
  记录锁是锁在索引记录上的锁。
- 间隙锁
  间隙锁是锁在索引记录间隙上的锁，或者锁在第一个索引记录之前的间隙，最后一个索引记录之后的间隙。
  间隙区间:(negative infinity,第一个索引记录),(第一个索引记录，第二个索引记录)。。。。(最后一个索引记录，positive infinity)
  举例:
  SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE; 会锁住区间(10,20),会阻止c1=15的插入,无论列是否存在这个值
  为什么引入间隙锁？
  性能和并发之间的平衡
  阻止其他事务插入数据到这个间隙当中
  适用的事务隔离级别:可重复读，(已提交读不适用)
  
  SELECT * FROM child WHERE uid = 100 for update;
  等值匹配
  uid是唯一索引，则触发的是记录锁
  uid存在非唯一索引，则触发间隙锁。
  uid不是索引字段，也则触发间隙锁，全表扫描
- 临键锁
  临键锁是记录锁和间隙锁的组合
- [[InnoDB锁实验]]
- 资料
  [官网文档mysql8锁](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-next-key-locks)