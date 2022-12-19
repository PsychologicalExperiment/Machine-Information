# MySQL

## Installation

* 安装mysql5.7

```
$ wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
$ sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm
$ cd /etc/yum.repos.d/
$ sudo yum install mysql-server
```

## Uninstall

* 卸载mysql

```shell
$ rpm -qa | grep -i mysql # 查看mysql
$ sudo yum remove mysql.xxxx
```

## 修改root密码

```sql
UPDATE user SET authentication_string = password ('qianhaiwaibao') WHERE User = 'root';
flush privileges;
```

## Q&A

* Unable to find a match: mysql-community-server

```shell
$ yum module disable mysql
$ yum -y install mysql-community-server
```

## 主从同步

## 配置主服务器

1. 主服务器设置log-bin和server-id，配置文件为/etc/my.cnf（配置文件路径可能不一致）

```
log-bin=xxx # binlog的名字，binlog保存在data_dir目录下，以.index或.00000*结尾
server-id=1
```

2. 主服务器创建一个账号给所有从服务器同步使用

```mysql
grant replication slave on *.* to 'slave'@'159.75.15.177' identified by 'qianhaiwaibao';
```

3. 刷新所有表，并上锁

```mysql
flush tables with read lock;
```

4. 获取二进制日志信息

```mysql
show master status;
```

5. 解锁库表

```mysql
unlock tables;
```

## 配置从服务器

1. 配置从服务器的配置文件

```mysql
[mysqld]
server-id=2
```

2. 配置同步参数

```mysql
mysql > CHANGE MASTER TO
-> MASTER_HOST='$IP',
-> MASTER_USER='$SLAVE_USER',
-> MASTER_PASSWORD='$PASSWORD',
-> MASTER_LOG_FILE='${BINLOGFILE}'
-> MASTER_LOG_POS=xx;
```

3. 开启主从同步

```mysql
mysql > start slave;
mysql > show slave status \G; # SLAVE_IO_RUNNING 和 SLAVE_SQL_RUNNING表
```

