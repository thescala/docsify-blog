## 布隆过滤器

布隆过滤器（Bloom filter）是一种非常节省空间的概率数据结构（space-efficient probabilistic data structure），运行速度快（时间效率），占用内存小（空间效率），但是有一定的误判率且无法删除元素。本质上由一个很长的二进制向量和一系列随机映射函数组成。

## 基于 `RedisBloom` 的布隆过滤器实现

### 1、下载 `RedisBloom`

```
1、git 下载
git clone https://github.com/RedisBloom/RedisBloom.git
cd redisbloom
make

2、wget 下载
wget https://github.com/RedisBloom/RedisBloom/archive/refs/tags/v2.2.4.tar.gz
tar -zxvf RedisBloom-2.2.4.tar.gz
cd RedisBloom-2.2.4/
make
```

### 2、修改 Redis Conf

```
[root@local ~]#vim /etc/redis.conf

# 在文件中添加下行

loadmodule /root/RedisBloom-2.2.4/redisbloom.so
```

### 3、启动 Redis server

```
[root@local ~]# /redis-server /etc/redis.conf

或者启动服务时加载os文件
[root@local ~]# /redis-server /etc/redis.conf --loadmodule /root/RedisBloom/redisbloom.so
```

### 4、api 使用

```xml
<!-- https://mvnrepository.com/artifact/com.redislabs/jrebloom -->
<dependency>
    <groupId>com.redislabs</groupId>
    <artifactId>jrebloom</artifactId>
    <version>2.1.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.6.0</version>
</dependency>

```

```java
package org.example.redisdemo;


import io.rebloom.client.Client;

import java.util.Arrays;

/**
 * @author admin
 */
public class RedisBloomDemo {
    public static void main(String[] args) {
        String userIdBloomKey = "userid";
        // 创建客户端，jedis实例
        Client client = new Client("localhost", 6379);
        // 创建一个有初始值和出错率的过滤器
        client.createFilter(userIdBloomKey,100000,0.01);
        // 新增一个<key,value>
        boolean userid1 = client.add(userIdBloomKey,"101310222");
        System.out.println("userid1 add " + userid1);

        // 批量新增values
        boolean[] booleans = client.addMulti(userIdBloomKey, "101310111", "101310222", "101310222");
        System.out.println("add multi result " + Arrays.toString(booleans));

        // 某个value是否存在
        boolean exists = client.exists(userIdBloomKey, "101310111");
        System.out.println("101310111 是否存在" + exists);

        //某批value是否存在
        boolean[] existsBoolean = client.existsMulti(userIdBloomKey, "101310111","101310222", "101310222","11111111");
        System.out.println("某批value是否存在 " + Arrays.toString(existsBoolean));
    }
}

```
