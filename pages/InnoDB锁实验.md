- InnoDB锁实验
  实验前置准备
- 环境
  mysql8，mac
- 建库建表语句，初始化数据
  ```
  # 建库
  CREATE DATABASE IF NOT EXISTS lock_test DEFAULT CHARSET utf8mb4;
  use lock_test;
  drop table if exists innodb_lock_test;
  # 建表
  CREATE TABLE IF NOT EXISTS `innodb_lock_test`(
  	`id` int(11) NOT NULL AUTO_INCREMENT, 
  	`user_id` int(11) NOT NULL, 
      `money` int(11) DEFAULT NULL, 
  	`user_name` varchar(30) NOT NULL, 
  	PRIMARY KEY(`id`), 
  	KEY `idx_uid`(`user_id`),
      KEY `idx_user_name`(`user_name`)
  )ENGINE=InnoDB AUTO_INCREMENT=97 DEFAULT CHARSET=utf8mb4;
  # 插入数据
  insert into innodb_lock_test(id,user_id,money,user_name)values
  (1,5,100,'aa'),(10,15,200,'bb'),
  (50,55,200,'cc'),(70,70,500,'dd'),(80,85,150,'bb');
  ```
- 背景知识
  1. 查看mysql session事务隔离级别命令
   ((62a704aa-f1a1-478d-a685-adc86f7be7d9))
  ```
  SELECT @@transaction_isolation;
  +-------------------------+
  | @@transaction_isolation |
  +-------------------------+
  | REPEATABLE-READ         |
  +-------------------------+
  ```
  2. 查询mysql事务对应锁信息命令
  ((62a743b4-a95b-4cde-ad09-cbe414c22306)) 
  ```
  select * from performance_schema.data_locks;
  ```
  3.select语句添加悲观锁
  select * from innodb_lock_test where id=1 for update;
  4.关闭自动提交事务
  ```
  begin;
  sql语句;
  ```
  select * from innodb_lock_test where id=1 for update;
  update innodb_lock_test set money=10000 where id=1;
  insert into innodb_lock_test(id,user_id,money,user_name)values(1,5,100,'aa');
- 实验步骤说明
  0. 前置条件:所有实验均处在可重复读事务隔离级别下
  ```
  SELECT @@transaction_isolation;
  +-------------------------+
  | @@transaction_isolation |
  +-------------------------+
  | REPEATABLE-READ         |
  +-------------------------+
  ```
  1. 第一个session窗口关闭自动提交事务，
  begin;
  更新语句;
  2. 查看事务锁信息
  3. 分析sql语句锁
  10. 第二个session窗口进行实验
  sql语句;
  11. 实验结果
  12. 实验结果分析
  a结果一 
  (1205, 'Lock wait timeout exceeded; try restarting transaction') 
  表示锁资源已经被第一个session锁住，等待锁资源超时
  b.结果二
  ```
   update innodb_lock_test set money=500 where id=1;
  6 rows in set
  Time: 0.009s
  ```
  锁资源没有被第一个session锁住,sql语句可以正常执行
  13. 实验结论
- 唯一索引字段实验
  单个等值匹配
  1第一个窗口执行命令
  ```
  begin;
  update innodb_lock_test set money=10000 where id=1;
  ```
  
  2.查看锁信息
  ```
  +--------+-----------------------------------------+-----------------------+-----------+----------+---------------+------------------+----------------+-------------------+------------+-----------------------+-----------+---------------+-------------+-----------+
  | ENGINE | ENGINE_LOCK_ID                          | ENGINE_TRANSACTION_ID | THREAD_ID | EVENT_ID | OBJECT_SCHEMA | OBJECT_NAME      | PARTITION_NAME | SUBPARTITION_NAME | INDEX_NAME | OBJECT_INSTANCE_BEGIN | LOCK_TYPE | LOCK_MODE     | LOCK_STATUS | LOCK_DATA |
  +--------+-----------------------------------------+-----------------------+-----------+----------+---------------+------------------+----------------+-------------------+------------+-----------------------+-----------+---------------+-------------+-----------+
  | INNODB | 140233293675848:1617:140233557539392    | 66497                 | 79        | 75       | lock_test     | innodb_lock_test | <null>         | <null>            | <null>     | 140233557539392       | TABLE     | IX            | GRANTED     | <null>    |
  | INNODB | 140233293675848:360:4:2:140233568248352 | 66497                 | 79        | 75       | lock_test     | innodb_lock_test | <null>         | <null>            | PRIMARY    | 140233568248352       | RECORD    | X,REC_NOT_GAP | GRANTED     | 1         |
  +--------+-----------------------------------------+-----------------------+-----------+----------+---------------+------------------+----------------+-------------------+------------+-----------------------+-----------+---------------+-------------+-----------+
  ```
  3.分析锁
  10.第二个session窗口进行实验
  ```
  # 失败
  select * from innodb_lock_test where id=1 for update;
  
  # 成功
  update innodb_lock_test set money=10000 where id=2;
  ```
  多个等值匹配
  
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点
- 非唯一索引字段实验
  单个等值匹配
  多个等值匹配
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点
- 非索引字段实验
  单个等值匹配
  多个等值匹配
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点