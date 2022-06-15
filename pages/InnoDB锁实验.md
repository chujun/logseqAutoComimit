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
- 前置学习
  1. 查看mysql session事务隔离级别命令
   ((62a704aa-f1a1-478d-a685-adc86f7be7d9))
  ```
  SELECT @@transaction_isolation;
  ```
  2. 查询mysql事务对应锁信息命令
  ```
  select * from performance_schema.data_locks;
  ```
- 唯一索引字段
  单个等值匹配
  多个等值匹配
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点
- 非唯一索引字段
  单个等值匹配
  多个等值匹配
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点
- 非索引字段
  单个等值匹配
  多个等值匹配
  范围匹配不存在满足条件数据
  范围匹配存在满足条件数据
  范围匹配存在满足条件数据包含左节点
  范围匹配存在满足条件数据包含右节点