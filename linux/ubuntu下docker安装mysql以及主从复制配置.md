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

### 2.做好准备工作

在系统上创建目录以及文件

```
/docker/mysql-jq/docker-compose.yml
/docker/mysql-jq/master1
/docker/mysql-jq/master1/my.cnf
/docker/mysql-jq/master1/Dockerfile
/docker/mysql-jq/master1/data
/docker/mysql-jq/master2
/docker/mysql-jq/master2/my.cnf
/docker/mysql-jq/master2/Dockerfile
/docker/mysql-jq/master2/data
/docker/mysql-jq/slave1
/docker/mysql-jq/slave1/my.cnf
/docker/mysql-jq/slave1/Dockerfile
/docker/mysql-jq/slave1/data
/docker/mysql-jq/slave2
/docker/mysql-jq/slave2/my.cnf
/docker/mysql-jq/slave2/Dockerfile
/docker/mysql-jq/slave2/data
```

写入docker-compose.yml文件如下：

```
version: '3'
services:
  mysql-master1:
    build:
      context: ./
      dockerfile: master/Dockerfile
    environment:
      - "MYSQL_ROOT_PASSWORD=123456"
      - "MYSQL_DATABASE=replicas_db"
    links:
      - mysql-slave
    ports:
      - "33066:3307"
    volumes:
       - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
       - /docker/mysql-jq/master/data:/var/lib/mysql/data # 映射数据库保存目录到宿主机，防止数据丢失
    restart: "no"  # 可以设置为always，关机自动重启
    hostname: mysql-master
  mysql-master2:
    build:
      context: ./
      dockerfile: master/Dockerfile
    environment:
      - "MYSQL_ROOT_PASSWORD=123456"
      - "MYSQL_DATABASE=replicas_db"
    links:
      - mysql-slave
    ports:
      - "33067:3307"
    volumes:
       - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
       - /docker/mysql-jq/master/data:/var/lib/mysql/data # 映射数据库保存目录到宿主机，防止数据丢失
    restart: "no"  # 可以设置为always，关机自动重启
    hostname: mysql-master
  mysql-slave1:
    build:
      context: ./
      dockerfile: slave/Dockerfile
    environment:
      - "MYSQL_ROOT_PASSWORD=123456"
      - "MYSQL_DATABASE=replicas_db"
    ports:
      - "33065:3307"
    volumes:
       - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
       - /docker/mysql-jq/slave/data:/var/lib/mysql/data # 映射数据库保存目录到宿主机，防止数据丢失
    restart: "no" # 可以设置为always,关机自动重启
    hostname: mysql-slave
  mysql-slave2:
    build:
      context: ./
      dockerfile: slave/Dockerfile
    environment:
      - "MYSQL_ROOT_PASSWORD=123456"
      - "MYSQL_DATABASE=replicas_db"
    ports:
      - "33065:3307"
    volumes:
       - /etc/localtime:/etc/localtime:ro # 设置容器时区与宿主机保持一致
       - /docker/mysql-jq/slave/data:/var/lib/mysql/data # 映射数据库保存目录到宿主机，防止数据丢失
    restart: "no" # 可以设置为always,关机自动重启
    hostname: mysql-slave
```

### 3.主数据库的配置

主数据库master1的DockerFile:

```
FROM mysql:8.0.13
MAINTAINER main master mysql`s image creation
ADD ./master1/my.cnf /etc/mysql/my.cnf
```

写入my.conf：

```
[mysqld]
## 设置server_id，一般设置为IP，注意要唯一
server_id=1
## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
binlog-ignore-db=mysql
## 开启二进制日志功能，可以随便取，最好有含义（关键就是这里了）
log-bin=replicas-mysql-bin
## 为每个session分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
## 主从复制的格式（mixed,statement,row，默认格式是statement）
binlog_format=mixed
## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062i
## 设置数据路径
datadir = /var/lib/mysql/data
```

### 4.从数据库的配置

同上

Dockerfile：

```
FROM mysql:8.0.13
MAINTAINER harrison
ADD ./slave/my.cnf /etc/mysql/my.cnf
```

my.conf：

[mysqld]
\## 设置server_id，一般设置为IP，注意要唯一
server_id=3
\## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
binlog-ignore-db=mysql
\## 开启二进制日志功能，以备Slave作为其它Slave的Master时使用
log-bin=replicas-mysql-slave1-bin
\## 为每个session 分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
\## 主从复制的格式（mixed,statement,row，默认格式是statement）
binlog_format=mixed
\## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
expire_logs_days=7
\## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
\## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
\## relay_log配置中继日志
relay_log=replicas-mysql-relay-bin
\## log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
\## 防止改变数据(除了特殊的线程)
read_only=1  i
\## 设置数据路径
datadir = /var/lib/mysql/data