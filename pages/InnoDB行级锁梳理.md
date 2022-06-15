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
  适用的事务隔离级别:可重复读，(已提交读不适用)
- 临键锁
- 资料
  [官网文档mysql](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-next-key-locks)