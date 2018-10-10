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
+---------------------------+--------------------------------+
| Variable_name             | Value                          |
+---------------------------+--------------------------------+
| log_slow_admin_statements | OFF                            |
| log_slow_slave_statements | OFF                            |
| slow_launch_time          | 2                              |
| slow_query_log            | OFF                            |
| slow_query_log_file       | /var/lib/mysql/master-slow.log |
+---------------------------+--------------------------------+
5 rows in set (0.00 sec)
```

