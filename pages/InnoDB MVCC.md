- 当前读(current read)也叫锁定读(Locking Reads)
  如下sql操作
  ```
  select ... lock in share mode
  select ... for update
  insert、update、delete 操作
  ```
- 快照读(snapshot read)也叫一致性非锁定读(Consistent Nonlocking Reads)
-