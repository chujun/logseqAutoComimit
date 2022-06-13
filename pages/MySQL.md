- 背景知识
  [[数据库]]
  [[字符集]]
-
- 数据库编码
  mysql的utf-8编码有两套实现
  
  utf8:(有点小坑)utf8编码只支持1-3个字节 。 在 utf8 编码中，中文是占 3 个字节，其他数字、英文、符号占一个字节。但 emoji 符号占 4 个字节，一些较复杂的文字、繁体字也是 4 个字节。
  utf8mb4:UTF-8 的完整实现，正版！最多支持使用 4 个字节表示字符，因此，可以用来存储 emoji 符号,一些复杂汉字，繁体字。
  更推荐使用这个字符集
  为什么有两套 UTF-8 编码实现呢？ 原因如下：
  ![image.png](../assets/image_1655106456807_0.png)
- MySQL 基础架构
  从下图你可以很清晰的看到客户端的一条 SQL 语句在 MySQL 内部是如何执行的。
  ![MySQL 的一个简要架构图.png](../assets/image_1655106994116_0.png)
  MySQL主要由下面几部分构成
  连接器： 身份认证和权限相关(登录 MySQL 的时候)。
  查询缓存： 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。
  分析器： 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。
  优化器： 按照 MySQL 认为最优的方案去执行。
  执行器： 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。
  插件式存储引擎 ： 主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎。
- MySQL 存储引擎
  MySQL 存储引擎采用的是插件式架构，支持多种存储引擎
  存储引擎是基于表的，而不是数据库。设置可以为表设置不同的存储引擎，当然实际业务开发中不可能这么使用。
	- MySQL主要存储引擎类型
	  [[InnoDB]]
	  MyISAM:MySQL 5.5.5 之前的默认存储引擎.
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
	  MySQL 当前默认的存储引擎是 InnoDB
	  所有的存储引擎中只有 InnoDB 是事务性存储引擎，也就是说只有 InnoDB 支持事务。
	- MySQL查看存储引擎命令
	  1. 通过 show variables like '%storage_engine%' 命令直接查看 MySQL 当前默认的存储引擎。
	  2. 如果你只想查看数据库中某个表使用的存储引擎的话，可以使用 show table status from db_name where name='table_name'命令。
	- MyISAM与InnoDB区别
- 开发建议
  1.【强制】不得使用外键与级联，一切外键概念必须在应用层解决。
  说明: 以学生和成绩的关系为例，学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外键。如果更新学生表中的 student_id，同时触发成绩表中的 student_id 更新，即为级联更新。外键与级联更新适用于单机低并发，不适合分布式、高并发集群; 级联更新是强阻塞，存在数据库更新风暴的风 险; 外键影响数据库的插入速度
  ---阿里巴巴开发手册
  对分库分表不友好 ：因为分库分表下外键是无法生效的。
  2. 【强制】禁止使用存储过程。
  存储过程难以调试和扩展，更没有可迁移性。
- [[MySQL命令]]