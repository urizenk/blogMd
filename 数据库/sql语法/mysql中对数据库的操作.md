1.对于数据库的创建：

create database if not exists `数据库名`  character set '字符集名字'; (推荐的方式，如果不存在就创建)

2.对于数据库的删除：

drop database if exists `数据库名` ;(推荐的删除方式，存在才删除)

3.对于数据库的修改：

alter database  `数据库名` {所作的修改}

4.对于数据库的使用：

查看所有的数据库：show databases；

查看当前正在使用的数据库：select database（）；

查看指定数据库下所有的表：show tables from `表名`;

查看数据库的创建信息: show create database `数据库名`;

使用一个数据库：use  `数据库名`;