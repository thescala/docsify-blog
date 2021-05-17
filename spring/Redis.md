### 一、缓存雪崩：

redis中大量缓存key在同一时间失效，造成大量请求落在数据库上导致数据库宕机。必须是大量请求同时进行访问，这时redis中大量缓存key过期失效。

解决方法：

1、缓存key过期时间时，添加随机时间，防止缓存key在同一时间失效。

2、设置缓存key永不过期

3、如果是redis集群部署，将缓存key分散到不同的redis库中

4、设置定时任务，在缓存key失效前重新刷进缓存

### 二、缓存穿透：

在高并发中，大量请求访问同一个不存在的缓存key时，导致请求落在数据库中，导致数据库查询压力过大而宕机。大量请求访问的是同一个缓冲key，而这个缓存key并不存在。

解决方式：

1、将不存在的请求数据缓存到redis中，并设置稍短的过期时间。

2、对参数进行校验，过滤非法参数

3、使用布隆过滤器进行过滤。

### 三、缓存击穿

在高并发中，某个热点缓存key在某个时刻突然失效，导致大量请求直接访问数据库致使数据库宕机。

解决方法：

1、设置热点永不过期

2、设置互斥锁，当大量请求访问时，将第一个请求加上互斥锁，其他请求拿不到锁就进行等待，当第一个请求拿到数据后就进行缓存数据，后面的请求就可以直接访问缓存了。

### 四、布隆过滤器

布隆过滤器（Bloom filter）是一种非常节省空间的概率数据结构（space-efficient probabilistic data structure），运行速度快（时间效率），占用内存小（空间效率），但是有一定的误判率且无法删除元素。本质上由一个很长的二进制向量和一系列随机映射函数组成。

### 五、Redis事务

#### 1、redis：

- Multi：标记事务的开始

- Exec：执行事务的commands队列

- Discard：结束事务，并清除commands队列

- redis默认不会开启事务，即command会立即执行，而不会排队，并不支持rollback

   

#### 2、redis客户端命令操作Redis事务

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

#### 3、SpringBoot操作Redis事务

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

###  六、分布式锁

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


