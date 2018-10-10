## 一、mysql手册
- [Centos6安装mysql5.6](https://github.com/Lancger/opslinux/blob/master/mysql/mysql5.6/centos6-one-install.md)
- [Centos6安装mysql5.7](https://github.com/Lancger/opslinux/blob/master/mysql/mysql5.6/centos6-one-install.md)

- [Centos7安装mysql5.6](https://github.com/Lancger/opslinux/blob/master/mysql/mysql5.6/centos7-one-install.md)
- [Centos7安装mysql5.7](https://github.com/Lancger/opslinux/blob/master/mysql/mysql5.6/centos7-one-install.md)

## 二、修改密码
```
#方式一
/usr/bin/mysqladmin -u root password '123456'

#方式二
mysql> set password=password('123456');

#方式三
mysql> use mysql
mysql> GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "root";
mysql> update user set Password = password('123456') where User='root';
mysql> show grants for root@"%";
mysql> flush privileges;
mysql> select Host,User,Password from user where User='root';
mysql> exit
```

## 三、查看表结构
```
mysql> desc user;

mysql> show create table user\G;

mysql> describe user;
```

## 四、查看参数变量
```
mysql> show variables like 'log_error';
+---------------+---------------------+
| Variable_name | Value               |
+---------------+---------------------+
| log_error     | /var/log/mysqld.log |
+---------------+---------------------+
1 row in set (0.01 sec)

mysql> show variables like '%slow%';
+---------------------------+--------------------------+
| Variable_name             | Value                    |
+---------------------------+--------------------------+
| log_slow_admin_statements | OFF                      |
| log_slow_slave_statements | OFF                      |
| slow_launch_time          | 2                        |
| slow_query_log            | ON                       |
| slow_query_log_file       | /var/log/mysqld-slow.log |
+---------------------------+--------------------------+
5 rows in set (0.00 sec)
```
## 五、模拟产生慢日志
```
mysql> select sleep(10) as a, 1 as b;
+---+---+
| a | b |
+---+---+
| 0 | 1 |
+---+---+
1 row in set (10.00 sec)

#查看慢日志
root># cat /var/log/mysqld-slow.log
/usr/sbin/mysqld, Version: 5.6.41-log (MySQL Community Server (GPL)). started with:
Tcp port: 3306  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
/usr/sbin/mysqld, Version: 5.6.41-log (MySQL Community Server (GPL)). started with:
Tcp port: 3306  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
/usr/sbin/mysqld, Version: 5.6.41-log (MySQL Community Server (GPL)). started with:
Tcp port: 3306  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
# Time: 181010 18:18:04
# User@Host: root[root] @ localhost []  Id:     2
# Query_time: 10.003351  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1539166684;
select sleep(10) as a, 1 as b;

```
