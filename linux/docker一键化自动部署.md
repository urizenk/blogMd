### 1. 安装docker-compose

进入github查看docker-compose版本 本次采用的版本是1.25.5

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

下载好将可执行文件应用于文件：

```
sudo chmod +x /usr/local/bin/docker-compose
```

验证：

```
docker-compose --version
```

### 2.构建目录结构

```
/docker/images/mysql-jq
/docker/images/redis-jq
/docker/images/nacos
/docker/images/sentinel
...
/docker/docker-compose.yml
```

### 3.搭建mysql双主双从集群

在mysql-jq目录下写入要构建镜像的Dockerfile：

```
FROM mysql:8.0.13
MAINTAINER master-jq

```

写入一个my.cnf文件：

```

[mysqld1]

port=3306

socket=etc/mysql/data/mysql.sock

default-character-set = utf8mb4

log-bin=mysql-bin

server-id=1

auto_increment_increment=2

auto_increment_offset=1

log-slave-updates

sync_binlog=1

[mysqld2]

log-bin=mysql-bin

server-id=2

auto_increment_increment=2

auto_increment_offset=2

log-slave-updates

sync_binlog=1
```

### 3.搭建redis集群

写入docker-compose.yml：

```
version: "3.7"
services: 
    redis:
        image: redis:latest
        container_name: redis
        command: /bin/bash -c "redis-server /usr/local/etc/redis/redis.conf"
        environment:
            REDIS_PASSWORD: 123456
        ports:
        - 6379:6379
        volumes:
        - type: bind
          source: /root/redis-cluster/redis
          target: /usr/local/etc/redis
        networks:
          redis-cluster-net:
            ipv4_address: 192.168.88.81
```

