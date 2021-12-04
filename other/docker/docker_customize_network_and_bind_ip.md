# docker 自定义网络并绑定 IP

## 创建自定义网络

```shell
$ docker network create --subnet=172.19.0.0/16 bind-net
```

## `docker run`方式创建容器并绑定 IP

```shell
docker run -ti -d -p 6379:6379 --name redis --net bind-net  --ip 172.19.0.5  -v /usr/local/docker/redis.conf:/etc/redis/redis.conf -v /usr/local/docker/data:/data -v /etc/localtime:/etc/localtime:ro --privileged=true --restart always redis:latest redis-server /etc/redis/redis.conf --appendonly yes
```

## `docker-compose`方式配置网络及 IP

```
version: '3'
services:
  redis:
    # 容器镜像
    image: redis:latest
    # 容器名称
    container_name: redis
    # 重启策略
    restart: always
    privileged: true
    appendonly: yes
    networks:
      bind-net:
        ipv4_address: 172.19.0.5
    ports:
      # 端口映射
      - '6379:6379'
    volumes:
      # 目录映射
      - ./config/redis/redis.conf:/usr/local/etc/redis/redis.conf:rw
      - ./config/data:/data:rw
    # 同步时区
    environment:
      - TZ=Asia/Shanghai
    # 在容器中执行的命令
    command: redis-server /usr/local/etc/redis/redis.conf

networks:
  bind-net:
    driver: bridge
    ipam:
      config:
       - subnet: 172.19.0.0/16
```
