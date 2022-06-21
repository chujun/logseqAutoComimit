- 查看服务端版本
  ```
  redis-server -v
  Redis server v=5.0.4 sha=00000000:0 malloc=libc bits=64 build=d4ba11298acbb366
  ```
- 查看和修改redis配置
  config get *：查看所有配置
  
  config get appendonly：查看指定配置
  ```
  127.0.0.1:6379> config get appendonly
  1) "appendonly"
  2) "no"
  ```
  临时设置：config set
  永久设置：config rewrite，将目前服务器的参数配置写入redis conf.