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
  
  你也可以通过 DISCARD 命令取消一个事务，它会清空事务队列中保存的所有命令。
  ```
  > MULTI
  OK
  > SET USER "Guide哥"
  QUEUED
  > GET USER
  QUEUED
  > DISCARD
  OK
  ```
  WATCH 命令用于监听指定的键，当调用 EXEC 命令执行事务时，如果一个被 WATCH 命令监视的键被修改的话，整个事务都不会执行，直接返回失败。
  ```
  > WATCH USER
  OK
  > MULTI
  > SET USER "Guide哥"
  OK
  > GET USER
  Guide哥
  > EXEC
  ERR EXEC without MULTI
  ```
- redis事务与数据库事务比较
  数据库事务支持ACID四大特性 ((62a6f8f4-7476-4657-98a0-da5603c8b194))
- redis事务问题
- 为什么redis开发者不支持完备的事务
- 如何解决redis事务问题