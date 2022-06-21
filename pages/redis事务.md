- 使用 Redis 事务
  Redis 可以通过 MULTI，EXEC，DISCARD 和 WATCH 等命令来实现事务(transaction)功能。
  ```
  > MULTI
  OK
  > SET USER "Guide哥"
  QUEUED
  > GET USER
  QUEUED
  > EXEC
  1) OK
  2) "Guide哥"
  ```
  使用 MULTI 命令后可以输入多个命令。Redis 不会立即执行这些命令，而是将它们放到队列，当调用了 EXEC 命令将执行所有命令。
  过程入下：
  1. 开始事务（MULTI）。
  2. 命令入队(批量操作 Redis 的命令，先进先出（FIFO）的顺序执行)。
  3. 执行事务(EXEC)。
- redis事务与数据库事务比较
- redis事务问题
- 为什么redis开发者不支持完备的事务
- 如何解决redis事务问题