- redis作为内存数据库，支持将数据持久化到磁盘。
  主要有两种持久化机制
- RDB持久化(快照 snapshotting)
  方案：通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。将这个数据库状态保存到RDB文件中。
  快照持久化是 Redis 默认采用的持久化方式，在 redis.conf 配置文件中默认有此下配置：
  ```
  save 900 1           #在900秒(15分钟)之后，如果至少有1个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  
  save 300 10          #在300秒(5分钟)之后，如果至少有10个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  
  save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，Redis就会自动触发BGSAVE命令创建快照。
  ```
  RDB 文件保存位置都是通过 dir 参数设置的，默认的文件名是 dump.rdb。
  执行方式
  a。手动执行
  b。根据服务器配置选项定期执行
  RDB文件保存工作
  SAVE:SAVE命令会阻塞Redis服务器进程，直到RDB文件创建完毕为止，在服务器进程阻塞期间，服务器不能处理任何命令请求.
  BGSAVE:会派生出一个子进程，然后由子进程负责创建RDB文件，服务器进程（父进程）继续处理命令请求
  RDB文件的载入工作
  RDB文件的载入工作是在服务器启动时自动执行的，所以Redis并没有专门用于载入RDB文件的命令，只要Redis服务器在启动时检测到RDB文件存在，它就会自动载入RDB文件
- AOF持久化(只追加文件，append only file)
  方案：开启 AOF 持久化后每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到内存缓存 server.aof_buf 中，然后再根据 appendfsync 配置来决定何时将其同步到硬盘中的 AOF 文件。
  与快照持久化相比
  AOF 持久化的实时性更好，因此已成为主流的持久化方案。
  默认情况下 Redis 没有开启 AOF（append only file）方式的持久化，可以通过 appendonly 参数开启：
  ```
  appendonly yes
  ```
  AOF 文件的保存位置和 RDB 文件的位置相同，都是通过 dir 参数设置的，默认的文件名是 appendonly.aof。
  AOF刷盘时机
  在 Redis 的配置文件中存在三种不同的 AOF 持久化方式，它们分别是：
  ```
  appendfsync always    #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
  appendfsync everysec  #每秒钟同步一次，显式地将多个写命令同步到硬盘
  appendfsync no        #让操作系统决定何时进行同步
  ```
  推荐参数选项：为了兼顾数据和写入性能，用户可以考虑 appendfsync everysec 选项 ，让 Redis 每秒同步一次 AOF 文件，Redis 性能几乎没受到任何影响。而且这样即使出现系统崩溃，用户最多只会丢失一秒之内产生的数据。当硬盘忙于执行写入操作的时候，Redis 还会优雅的放慢自己的速度以便适应硬盘的最大写入速度。
- AOF 重写
  目的:为了解决AOF文件体积膨胀的问题，Redis提供了AOF文件重写（rewrite）功能。
  Redis服务器可以创建一个新的AOF文件来替代现有的AOF文件，新旧两个AOF文件所保存的数据库状态相同，但新AOF文件不会包含任何浪费空间的冗余命令，
	- 好处:新AOF文件的体积通常会比旧AOF文件的体积要小得多。
	- 功能:AOF文件重写并不需要对现有的AOF文件进行任何读取、分析或者写入操作，这个功能是通过读取服务器当前的数据库状态来实现的。
	- 实现原理:
	  AOF重写缓冲区
	  在执行 BGREWRITEAOF 命令时，Redis 服务器会维护一个 AOF 重写缓冲区，该缓冲区会在子进程创建新 AOF 文件期间，记录服务器执行的所有写命令。当子进程完成创建新 AOF 文件的工作之后，服务器会将重写缓冲区中的所有内容追加到新 AOF 文件的末尾，使得新的 AOF 文件保存的数据库状态与现有的数据库状态一致。最后，服务器用新的 AOF 文件替换旧的 AOF 文件，以此来完成 AOF 文件重写操作。
	- redis主进程状态
	  AOF重写：redis会派生出一个子进程，然后由子进程负责AOF重写，服务器进程（父进程）继续处理命令请求。
	- 解决数据不一致的办法
	  数据不一致情况描述：
	  因为子进程在进行AOF重写期间，服务器进程还需要继续处理命令请求，而新的命令可能会对现有的数据库状态进行修改，从而使得服务器当前的数据库状态和重写后的AOF文件所保存的数据库状态不一致。
	  ![截屏2022-06-21 下午9.52.42.png](../assets/截屏2022-06-21_下午9.52.42_1655819579043_0.png)
	- 简化命令demo
	  ![截屏2022-06-21 下午9.39.55.png](../assets/截屏2022-06-21_下午9.39.55_1655818881493_0.png)
	  如果服务器想要用尽量少的命令来记录list键的状态，那么最简单高效的办法不是去读取和分析现有AOF文件的内容，
	  而是直接从数据库中读取键list的值，然后用一条RPUSH list"C""D""E""F""G"命令来代替保存在AOF文件中的六条命令，
	  这样就可以将保存list键所需的命令从六条减少为一条了。
	- AOF重写过程伪代码
	  ```
	  def aof_rewrite(new_aof_file_name):
	      #创建新 AOF文件
	      f = create_file(new_aof_file_name)
	      #遍历数据库
	      for db in redisServer.db:
	          #忽略空数据库
	          if db.is_empty(): continue
	          #写入SELECT命令，指定数据库号码
	          f.write_command("SELECT" + db.id)
	          #遍历数据库中的所有键
	          for key in db:
	              #忽略已过期的键
	              if key.is_expired(): continue
	              #根据键的类型对键进行重写
	              if key.type == String:
	                  rewrite_string(key)
	              elif key.type == List:
	                  rewrite_list(key)
	              elif key.type == Hash:
	                  rewrite_hash(key)
	              elif key.type == Set:
	                  rewrite_set(key)
	              elif key.type == SortedSet:
	                  rewrite_sorted_set(key)
	              #如果键带有过期时间，那么过期时间也要被重写
	              if key.have_expire_time():
	                  rewrite_expire_time(key)
	          #写入完毕，关闭文件
	          f.close()
	      def rewrite_string(key):
	          #使用GET命令获取字符串键的值
	          value = GET(key)
	          #使用SET命令重写字符串键
	          f.write_command(SET, key, value)
	      def rewrite_list(key):
	          #使用LRANGE命令获取列表键包含的所有元素
	          item1, item2, ..., itemN = LRANGE(key, 0, -1)
	          #使用RPUSH命令重写列表键
	          f.write_command(RPUSH, key, item1, item2, ..., itemN)
	      def rewrite_hash(key):
	          #使用HGETALL命令获取哈希键包含的所有键值对
	          field1, value1, field2, value2, ..., fieldN, valueN = HGETALL(key)
	          #使用HMSET命令重写哈希键
	          f.write_command(HMSET, key, field1, value1, field2, value2, ..., fieldN, valueN)
	      def rewrite_set(key);
	          #使用SMEMBERS命令获取集合键包含的所有元素
	          elem1, elem2, ..., elemN = SMEMBERS(key)
	          #使用SADD命令重写集合键
	          f.write_command(SADD, key, elem1, elem2, ..., elemN)
	      def rewrite_sorted_set(key):
	          #使用ZRANGE命令获取有序集合键包含的所有元素
	          member1, score1, member2, score2, ..., memberN, scoreN = ZRANGE(key, 0, -1, "WITHSCORES")
	          #使用ZADD命令重写有序集合键
	          f.write_command(ZADD, key, score1, member1, score2, member2, ..., scoreN, memberN)
	      def rewrite_expire_time(key):
	          #获取毫秒精度的键过期时间戳
	          timestamp = get_expire_time_in_unixstamp(key)
	          #使用PEXPIREAT命令重写键的过期时间
	          f.write_command(PEXPIREAT, key, timestamp)
	  ```
-