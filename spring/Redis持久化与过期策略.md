## redis持久化方式

### 1、持久化命令
* 在RDB持久化模式中我们可以使用`save`和`bgsave`命令进行数据持久化操作
* 在AOF持久化模式中使用`rewriteaof`和`bgrewriteaof`命令进行持久化操作

### 2、RDB持久化方案

#### 2.1、RDB相关配置
```conf
# 设置快照文件的存放路径，这个配置项一定是个目录
dir ./
# 设置快照的文件名，默认是 dump.rdb
dbfilename dump.rdb
```
#### 2.2、RDB持久化配置策略
```conf
# 如果不行使用rdb持久化 则注释掉所有save行，也可以直接重写成 
save ""
# ----------------------------------------------------- 
# 表示900 秒内如果至少有 1 个 key 的值变化，则保存
save 900 1
# 表示300 秒内如果至少有 10 个 key 的值变化，则保存
save 300 10
# 表示60 秒内如果至少有 10000 个 key 的值变化，则保存
save 60 10000
# 默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了
stop-writes-on-bgsave-error yes
# 默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。
rdbcompression yes
# 默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。
rdbchecksum yes
```

#### 2.3、停止rdb持久化

`redis-cli config set save ""`

### 3、AOF持久化方案

#### 3.1、AOF相关配置
```conf
# 是否打开 AOF 持久化功能
appendonly yes
# AOF 文件名称
appendfilename "appendonly.aof"
# 同步频率 同步三种策略
# always：同步持久化,redis执行每个写命令时，都同步写入硬盘
# everysec：异步操作,每秒执行一次，显示的在这一秒内执行的写命令同步到硬盘,如果一秒钟内宕机，有数据丢失
# no：不同步到硬盘（让操作系统来决定何时进行同步）(将缓存回写的策略交给系统，linux 默认是30秒将缓冲区的数据回写硬盘)
appendfsync everysec
```
#### 3.2、AOF的重写

定义：AOF采用文件追加的方式持久化数据，所以文件会越来越大，为了避免这种情况发生，增加了重写机制

当AOF文件的大小超过了配置所设置的阙值时，Redis就会启动AOF文件压缩，只保留可以恢复数据的最小指令集，可以使用命令`bgrewriteaof`

触发机制：Redis会记录上次重写时的AOF文件大小，默认配置时当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

```conf
# aof重写策略
no-appendfsync-on-rewrite no
# 当前AOF文件大小和上一次重写时AOF文件大小的比值
auto-aof-rewrite-percentage 100 # （一倍）
#文件的最小体积 当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发
auto-aof-rewrite-min-size 64mb
# 默认值是yes，在写入AOF文件时，突然断电写了一半，设置成yes会log继续，如果设置成no，就直接恢复失败了
aof-load-truncated yes
```

#### 4、AOF与RDB比较

1. aof文件比rdb更新频率高，优先使用aof还原数据。
2. aof比rdb更安全也更大
3. rdb性能比aof好
4. 如果两个都配了优先加载AOF

## key过期淘汰机制
1. 定期删除
```conf
# 默认10.表示1s执行10次定期删除，即每隔100ms执行一次，可以修改这个配置值。
hz 10
```
Redis默认是每隔100ms,随机检查设置了过期的key并删除已过期的key；维护定时器消耗CPU资源；

具体来说，Redis 每秒执行 10 次：

* 从具有关联过期的密钥集中测试 20 个随机密钥。
* 删除所有发现过期的密钥。
* 如果超过 25% 的密钥已过期，请从步骤 1 重新开始。

> 为什么是随机抽取部分检测，而不是全部？
>
> 因为如果Redis里面有大量key都设置了过期时间，全部都去检测一遍的话CPU负载就会很高，会浪费大量的时间在检测上面，甚至直接导致redis挂掉。所有只会抽取一部分而不会全部检查。
   
2.惰性删除
```conf
# 默认为5 随机采样
maxmemory-samples 5
```
当key被访问时检查该key的过期时间，若已过期则删除；已过期未被访问的数据仍保持在内存中，消耗内存资源

> RDB和AOF持久化命令（`save`和`bgsave`，`rewriteaof`和`bgrewriteaof`）都不会将过期key持久化到RDB文件或AOF文件中，可以保证重启服务时不会将过期key载入Redis。

3、内存淘汰机制

```conf
# maxmemory <bytes>
# 以下均合法
maxmemory 1024000
maxmemory 1GB
maxmemory 1G
maxmemory 1024KB
maxmemory 1024K
maxmemory 1024MB
```

当内存达到限制时，Redis 具体的回收策略是通过 maxmemory-policy 配置项配置的。
```conf
maxmemory-policy noeviction
```
以下的策略都是可用的：
```conf
# volatile-lru -> Evict using approximated LRU, only keys with an expire set.
# allkeys-lru -> Evict any key using approximated LRU.
# volatile-lfu -> Evict using approximated LFU, only keys with an expire set.
# allkeys-lfu -> Evict any key using approximated LFU.
# volatile-random -> Remove a random key having an expire set.
# allkeys-random -> Remove a random key, any key.
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
# noeviction -> Don't evict anything, just return an error on write operations
```

|回收策略|说明|
|---|---|
|noeviction| 当内存不足以容纳新写入数据时，新写入操作会报错，无法写入新数据，一般不采用。(当内存达到设置的最大值时，所有申请内存的操作都会报错(如set,lpush等)，只读操作如get命令可以正常执行)|
|allkeys-lru| 当内存不足以容纳新写入数据时，移除最近最少使用的key，这个是最常用的|
|allkeys-random| 当内存不足以容纳新写入的数据时，随机移除key(所有key使用随机淘汰)|
|allkeys-lfu| 当内存不足以容纳新写入数据时，移除最不经常（最少）使用的key|
|volatile-lru| 当内存不足以容纳新写入数据时，在设置了过期时间的key中，移除最近最少使用的key。|
|volatile-random| 内存不足以容纳新写入数据时，在设置了过期时间的key中，随机移除某个key 。|
|volatile-lfu| 当内存不足以容纳新写入数据时，在设置了过期时间的key中，移除最不经常（最少）使用的key|
|volatile-ttl| 当内存不足以容纳新写入数据时，在设置了过期时间的key中，优先移除过期时间最早（剩余存活时间最短）的key。|

> LRU算法
> 
> LRU（Least Recently Used）表示最近最少使用，该算法根据数据的历史访问记录来进行淘汰数据，其核心思想是“如果数据最近被访问过，那么将来被访问的几率也更高”。
> 
> LFU算法
> 
> LFU（Least Frequently Used）表示最不经常使用，它是根据数据的历史访问频率来淘汰数据，其核心思想是“如果数据过去被访问多次，那么将来被访问的频率也更高”。

其他配置
```conf
# Eviction processing is designed to function well with the default setting.
# If there is an unusually large amount of write traffic, this value may need to
# be increased.  Decreasing this value may reduce latency at the risk of 
# eviction processing effectiveness
#   0 = minimum latency, 10 = default, 100 = process without regard to latency
maxmemory-eviction-tenacity 10
 
# replica节点会忽略maxmemory不执行删除
# Starting from Redis 5, by default a replica will ignore its maxmemory setting
replica-ignore-maxmemory yes

# 取值的范围是1到10，redis清理过期的key是需要消耗内存的，所以可以设置的过大可以让过期数据清理的更干净,但是同时让redis牺牲更多的CPU取清理更多的过期key，需要权衡
active-expire-effort 1
```
参考：

1、https://www.redis.com.cn/

2、https://blog.csdn.net/wsdc0521/category_9887941.html