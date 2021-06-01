
### Redis事务

### 1、redis：

- Multi：标记事务的开始

- Exec：执行事务的commands队列

- Discard：结束事务，并清除commands队列

- redis默认不会开启事务，即command会立即执行，而不会排队，并不支持rollback

   

### 2、redis客户端命令操作Redis事务

```sh
redis 127.0.0.1:6379> MULTI
OK
redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED
redis 127.0.0.1:6379> GET book-name
QUEUED
redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
redis 127.0.0.1:6379> SMEMBERS tag
QUEUED
redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programmin
```

### 3、SpringBoot操作Redis事务

```java
public void setString(String key, Object object) {
    stringRedisTemplate.setEnableTransactionSupport(true);
    // 开启事务
    stringRedisTemplate.multi();
    try {
        // 如果是String 类型
        String value = (String) object;
        stringRedisTemplate.opsForValue().set(key, value);
    } catch (Exception e) {
        // 回滚
        stringRedisTemplate.discard();
    } finally {
        // 提交
        stringRedisTemplate.exec();
    }
}
```

### 4、分布式锁

```java
//设置锁
public boolean tryLock_with_lua(String key, String UniqueId, int seconds) {
    String lua_scripts = "if redis.call('setnx',KEYS[1],ARGV[1]) == 1 then" +
            "redis.call('expire',KEYS[1],ARGV[2]) return 1 else return 0 end";
    List<String> keys = new ArrayList<>();
    List<String> values = new ArrayList<>();
    keys.add(key);
    values.add(UniqueId);
    values.add(String.valueOf(seconds));
    Object result = jedis.eval(lua_scripts, keys, values);
    //判断是否成功
    return result.equals(1L);
}
//释放锁
public boolean releaseLock_with_lua(String key,String value) {
    String luaScript = "if redis.call('get',KEYS[1]) == ARGV[1] then " +
            "return redis.call('del',KEYS[1]) else return 0 end";
    return jedis.eval(luaScript, Collections.singletonList(key), Collections.singletonList(value)).equals(1L);
}

```
