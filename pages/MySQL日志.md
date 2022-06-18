- MySQL日志主要包括错误日志、查询日志、慢查询日志、事务日志、二进制日志几大类。
  其中，比较重要的还要属二进制日志 binlog（归档日志）和事务日志 redo log（重做日志）和 undo log（回滚日志）。
  ![image.png](../assets/image_1655515764255_0.png)
- binlog二进制日志
  物理日志，记录内容是“在某个数据页上做了什么修改”，属于 InnoDB 存储引擎。
- redolog重做日志
- MySQL两阶段提交
- undolog回滚日志
- 慢查询日志
-