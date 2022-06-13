- 查看变量
  show variables
  支持通配符查询
  主要例子
  查询存储引擎
  show variables like '%storage_engine%'
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
- select version() 命令查看你的 MySQL 版本
  ```
  +-----------+
  | version() |
  +-----------+
  | 8.0.22    |
  +-----------+
  ```
-