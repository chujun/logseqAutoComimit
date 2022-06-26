- redis info命令
  id:: 62b860bd-9158-4eca-9c91-f7af917427b6
  [info 命令官网说明](https://redis.io/commands/info/)
  ```
  127.0.0.1:6379> info
  # Server
  redis_version:5.0.4
  redis_git_sha1:00000000
  redis_git_dirty:0
  redis_build_id:d4ba11298acbb366
  redis_mode:standalone
  os:Darwin 20.6.0 x86_64
  arch_bits:64
  multiplexing_api:kqueue
  atomicvar_api:atomic-builtin
  gcc_version:4.2.1
  process_id:4661
  run_id:9e469e67fffeada53c4f0d84fd9f1e7f3323eee6
  tcp_port:6379
  uptime_in_seconds:7302
  uptime_in_days:0
  hz:10
  configured_hz:10
  lru_clock:12083414
  executable:/Users/chujun/my/redis-server
  config_file:
  
  # Clients
  connected_clients:1
  client_recent_max_input_buffer:2
  client_recent_max_output_buffer:0
  blocked_clients:0
  
  # Memory
  used_memory:1052864
  used_memory_human:1.00M
  used_memory_rss:1347584
  used_memory_rss_human:1.29M
  used_memory_peak:1052864
  used_memory_peak_human:1.00M
  used_memory_peak_perc:100.00%
  used_memory_overhead:1037702
  used_memory_startup:987488
  used_memory_dataset:15162
  used_memory_dataset_perc:23.19%
  allocator_allocated:1006848
  allocator_active:1309696
  allocator_resident:1309696
  total_system_memory:8589934592
  total_system_memory_human:8.00G
  used_memory_lua:37888
  used_memory_lua_human:37.00K
  used_memory_scripts:0
  used_memory_scripts_human:0B
  number_of_cached_scripts:0
  maxmemory:0
  maxmemory_human:0B
  maxmemory_policy:noeviction
  allocator_frag_ratio:1.30
  allocator_frag_bytes:302848
  allocator_rss_ratio:1.00
  allocator_rss_bytes:0
  rss_overhead_ratio:1.03
  rss_overhead_bytes:37888
  mem_fragmentation_ratio:1.34
  mem_fragmentation_bytes:340736
  mem_not_counted_for_evict:0
  mem_replication_backlog:0
  mem_clients_slaves:0
  mem_clients_normal:49694
  mem_aof_buffer:0
  mem_allocator:libc
  active_defrag_running:0
  lazyfree_pending_objects:0
  
  # Persistence
  loading:0
  rdb_changes_since_last_save:0
  rdb_bgsave_in_progress:0
  rdb_last_save_time:1656243280
  rdb_last_bgsave_status:ok
  rdb_last_bgsave_time_sec:-1
  rdb_current_bgsave_time_sec:-1
  rdb_last_cow_size:0
  aof_enabled:0
  aof_rewrite_in_progress:0
  aof_rewrite_scheduled:0
  aof_last_rewrite_time_sec:-1
  aof_current_rewrite_time_sec:-1
  aof_last_bgrewrite_status:ok
  aof_last_write_status:ok
  aof_last_cow_size:0
  
  # Stats
  total_connections_received:1
  total_commands_processed:9
  instantaneous_ops_per_sec:0
  total_net_input_bytes:284
  total_net_output_bytes:16879
  instantaneous_input_kbps:0.00
  instantaneous_output_kbps:0.00
  rejected_connections:0
  sync_full:0
  sync_partial_ok:0
  sync_partial_err:0
  expired_keys:0
  expired_stale_perc:0.00
  expired_time_cap_reached_count:0
  evicted_keys:0
  keyspace_hits:0
  keyspace_misses:0
  pubsub_channels:0
  pubsub_patterns:0
  latest_fork_usec:0
  migrate_cached_sockets:0
  slave_expires_tracked_keys:0
  active_defrag_hits:0
  active_defrag_misses:0
  active_defrag_key_hits:0
  active_defrag_key_misses:0
  
  # Replication
  role:master
  connected_slaves:0
  master_replid:e2a4599b27b4eda88bdecab26eb353bfbe7052db
  master_replid2:0000000000000000000000000000000000000000
  master_repl_offset:0
  second_repl_offset:-1
  repl_backlog_active:0
  repl_backlog_size:1048576
  repl_backlog_first_byte_offset:0
  repl_backlog_histlen:0
  
  # CPU
  used_cpu_sys:3.345934
  used_cpu_user:2.031551
  used_cpu_sys_children:0.000000
  used_cpu_user_children:0.000000
  
  # Cluster
  cluster_enabled:0
  
  # Keyspace
  db0:keys=8,expires=0,avg_ttl=0
  db1:keys=1,expires=0,avg_ttl=0
  ```
- 查看服务端版本
  ```
  redis-server -v
  Redis server v=5.0.4 sha=00000000:0 malloc=libc bits=64 build=d4ba11298acbb366
  ```
- 查看和修改redis配置
  id:: 62afcdb5-b39a-4b65-9826-903cb6fb48e2
  config get *：查看所有配置
  
  config get appendonly：查看指定配置
  ```
  127.0.0.1:6379> config get appendonly
  1) "appendonly"
  2) "no"
  ```
  临时设置：config set
  永久设置：config rewrite，将目前服务器的参数配置写入redis conf。
-