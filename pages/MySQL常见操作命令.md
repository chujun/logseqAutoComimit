- 数据库操作
  ```
  /* 数据库操作 */ ------------------
  -- 查看当前数据库
      SELECT DATABASE();
  -- 显示当前时间、用户名、数据库版本
      SELECT now(), user(), version();
  -- 创建库
      CREATE DATABASE[ IF NOT EXISTS] 数据库名 数据库选项
      数据库选项：
          CHARACTER SET charset_name
          COLLATE collation_name
  -- 查看已有库
      SHOW DATABASES[ LIKE 'PATTERN']
  -- 查看当前库信息
      SHOW CREATE DATABASE 数据库名
  -- 修改库的选项信息
      ALTER DATABASE 库名 选项信息
  -- 删除库
      DROP DATABASE[ IF EXISTS] 数据库名
          同时删除该数据库相关的目录及其目录内容
  ```
- 在线快速查询手册网站
  ![截屏2022-06-17 上午8.56.53.png](../assets/截屏2022-06-17_上午8.56.53_1655427427069_0.png) 
  [一千行 MySQL 学习笔记](https://javaguide.cn/database/mysql/a-thousand-lines-of-mysql-study-notes.html#%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C)
- 参考资料
  类似于一种在线快速查询手册网站
  [一千行 MySQL 学习笔记](https://javaguide.cn/database/mysql/a-thousand-lines-of-mysql-study-notes.html#%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C)