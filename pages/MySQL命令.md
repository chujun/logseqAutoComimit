- 查看变量
  show variables
  支持通配符查询
  主要例子
  查询存储引擎
  show variables like '%storage_engine%'
- select version() 命令查看你的 MySQL 版本
  ```
  +-----------+
  | version() |
  +-----------+
  | 8.0.22    |
  +-----------+
  ```
- 查看字符集
  SHOW CHARSET
  ![SHOW CHARSET.png](../assets/image_1655106043207_0.png)
- 查看支持的存储引擎
  show engines
  ```
  +--------------------+---------+----------------------------------------------------------------+--------------+--------+------------+
  | Engine             | Support | Comment                                                        | Transactions | XA     | Savepoints |
  +--------------------+---------+----------------------------------------------------------------+--------------+--------+------------+
  | ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO     | NO         |
  | BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO     | NO         |
  | MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO     | NO         |
  | FEDERATED          | NO      | Federated MySQL storage engine                                 | <null>       | <null> | <null>     |
  | MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO     | NO         |
  | PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO     | NO         |
  | InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES    | YES        |
  | MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO     | NO         |
  | CSV                | YES     | CSV storage engine                                             | NO           | NO     | NO         |
  +--------------------+---------+----------------------------------------------------------------+--------------+--------+------------+
  ```
- 查询线程运行情况
- 查询MySQL隔离级别命令
  id:: 62a704aa-f1a1-478d-a685-adc86f7be7d9
  MySQL8之前：SELECT @@tx_isolation;
  MySQL 8.0 该命令改为SELECT @@transaction_isolation;
  ```shell
  mysql root@localhost:(none)> SELECT @@transaction_isolation;
  +-------------------------+
  | @@transaction_isolation |
  +-------------------------+
  | REPEATABLE-READ         |
  +-------------------------+
  ```
  修改mysql事务隔离级别命令
  ```
  //修改事务隔离级别为已提交读
  set session transaction isolation level read committed;
  //修改mysql事务隔离级别为可重复读
  set session transaction isolation level repeatable read; 
  ```
- 事务
  查询当前运行的所有事务
  ```
  select * from information_schema.innodb_trx;
  ```
  
  session窗口关闭自动提交事务
  ```
  set autocommit=0;
  ```
- 查询锁信息
  id:: 62a743b4-a95b-4cde-ad09-cbe414c22306
  查询锁信息
  ```
  # mysql8之前
   SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
  # mysql8之后
  select * from performance_schema.data_locks;
  ```
  
  LOCK_MODE列说明
  IX:意向排他锁
  X:
  X,REC_NOT_GAP:非间隙排他锁
  X,GAP:间隙排他锁
  LOCK_DATA列说明
  supremum pseudo-record :表示MySQL决定锁定最大间隙范围
  这儿的表锁相当于对所有记录加了记录锁和最大范围的间隙锁。