### 1.下载Mysql服务器

`sudo apt-get update`

`sudo apt-get install mysql-server`

### 2.进入Mysql文件夹下获取用户名以及密码

`sudo vim /etc/mysql/debian.cnf获取MySQL的用户名以及密码`

```
user     = debian-sys-maint
password = GicCpNIaAywtq2HF
```



### 3.修改MySQL的用户名以及密码

更改root用户的认证插件：

`update mysql.user set plugin = "caching_sha2_password" where user = "root";`

将root用户认证字符串置空：

`update mysql.user set authentication_string="", plugin="caching_sha2_password" where user="root";`

查看并更改每个用户对应的主机名字：

` select user, host from user;`

`update mysql.user set host = "%" where user = "root";`

更改root用户的密码并且刷新权限：

alter user 'root'@'%' identified by '123dot';

FLUSH PRIVILEGES;

### 4.做一些相关性的配置

#### 1.在全局配置里面将外部ip绑定设为任何

`sudo vim /etc/mysql/my.cnf`



写入如下信息：

`[mysqld]
bind-address = 0.0.0.0`



#### 2.修改所有复制机器的uuid

```
sudo vim /var/lib/mysql/auto.cnf
```

保证各不相同即可。

### 5.配置MySQL双主双从

在master1主机1上进入MySQL服务器配置：

```
sudo vim /etc/mysql/my.cnf
```

写入如下的配置：

```
# bin log 日志
log-bin=/var/lib/mysql/binlog
# 服务id
server-id=1
#主从复制忽略的数据库
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
#开启主从复制的数据库
binlog-do-db=mydb2
# bin log 日志格式
#STATEMENT:记录主库执行的SQL复制到从库; 调用时间函数时会导致主从数据不一致
#ROW:记录主库每一行的变化;效率低
#MIXED:修复一些主从数据不一致情况;本地变量调用还会存在问题;@@hostname
binlog_format=statement
#二进制日志自动删除/过期的天数。默认值为0，表示不自动删除
expire_logs_days=7
#跳过主从复制中遇到的所有错误或指定类型的错误
slave_skip_errors=1062
#在作为从数据库时候，有写入操作也要更新二进制日志文件
log-slave-updates
#标识自增长字段每次递增的量，也就是步长
auto-increment-increment=2
#表示自增长从哪个数开始
auto-increment-offset=1

```

在master2主机上进入MySQL服务器配置：

```
sudo vim /etc/mysql/my.cnf
```

写入如下的配置：

```
# bin log 日志
log-bin=/var/lib/mysql/binlog
# # 服务id
server-id=3
# #主从复制忽略的数据库
binlog-ignore-db=mysql
binlog-ignore-db=information_schema
# #开启主从复制的数据库
binlog-do-db=mydb2
# # bin log 日志格式
# #STATEMENT:记录主库执行的SQL复制到从库; 调用时间函数时会导致主从数据不一致
# #ROW:记录主库每一行的变化;效率低
# #MIXED:修复一些主从数据不一致情况;本地变量调用还会存在问题;@@hostname
binlog_format=statement
# #二进制日志自动删除/过期的天数。默认值为0，表示不自动删除
expire_logs_days=7
# #跳过主从复制中遇到的所有错误或指定类型的错误
slave_skip_errors=1062
# #在作为从数据库时候，有写入操作也要更新二进制日志文件
log-slave-updates
# #标识自增长字段每次递增的量，也就是步长
auto-increment-increment=2
# #表示自增长从哪个数开始
auto-increment-offset=2

```

在slave1主机中进入MySQL服务器配置，写下：

```
#从服务器唯一ID
server-id=2
#启用中继日志
relay-log=mysql-relay
```

slave2中写入：

```
server-id=4
#启用中继日志
relay-log=mysql-relay
```

重启MySQL：

```
systemctl restart mysql.service
```

查看master的状态：

```
show master status;
```

对主机1和主机2分别创建主主同步账号和主从同步账号：

```
CREATE USER 'repl_user'@'%' IDENTIFIED WITH mysql_native_password BY '123dot';
CREATE USER 'slave_sync_user'@'%' IDENTIFIED WITH mysql_native_password BY '123dot';
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'%';
GRANT REPLICATION SLAVE ON *.* TO 'slave_sync_user'@'%';
```

查看主机1，2的binlog和偏移量，并在从机上输入：

```
CHANGE MASTER TO MASTER_HOST='192.168.0.101', MASTER_USER='slave_sync_user', MASTER_PASSWORD='123dot', MASTER_LOG_FILE='binlog.000015', MASTER_LOG_POS=157;
CHANGE MASTER TO MASTER_HOST='192.168.0.102', MASTER_USER='slave_sync_user', MASTER_PASSWORD='123dot', MASTER_LOG_FILE='binlog.000015', MASTER_LOG_POS=157;
```

配置主机1与主机2的复制，在主机2中写入：

```
CHANGE MASTER TO MASTER_HOST='192.168.0.101', MASTER_USER='repl_user', MASTER_PASSWORD='123dot', MASTER_LOG_FILE='binlog.000015', MASTER_LOG_POS=157;
```

