- MySQL日志主要包括错误日志、查询日志、慢查询日志、事务日志、二进制日志几大类。
  其中，比较重要的还要属二进制日志 binlog（归档日志）和事务日志 redo log（重做日志）和 undo log（回滚日志）。
  ![image.png](../assets/image_1655515764255_0.png)
- binlog二进制日志
  binlog会记录所有涉及更新数据的逻辑操作，并且是顺序写。
  
  binlog作用
  MySQL数据库的数据备份、主备、主主、主从都离不开binlog，需要依靠binlog来同步数据，保证数据一致性。
  ![image.png](../assets/image_1655516219834_0.png) 
  binlog记录格式
  binlog 日志有三种格式，可以通过binlog_format参数指定。statement，row，mixed
  ```
  show variables like 'binlog_format';
  +---------------+-------+
  | Variable_name | Value |
  +---------------+-------+
  | binlog_format | ROW   |
  +---------------+-------+
  ```
  statement
  记录的内容是SQL语句原文,比如执行一条update T set update_time=now() where id=1，记录的内容如下。
  ![image.png](../assets/image_1655516461053_0.png) 
  statement缺点:原始sql语句数据同步时可能导致不一致性，例如now()
  row
  记录的内容不再是简单的SQL语句了，还包含操作的具体数据
  mixed
  binlog和redolog比较
  redolog是物理日志，记录内容是“在某个数据页上做了什么修改”，属于 InnoDB 存储引擎。
   binlog 是逻辑日志，记录内容是语句的原始逻辑，类似于“给 ID=2 这一行的 c 字段加 1”，属于MySQL Server 层。不管用什么存储引擎，只要发生了表数据更新，都会产生 binlog 日志。
-
-
-
- redolog重做日志
- MySQL两阶段提交
- undolog回滚日志
- 慢查询日志
-