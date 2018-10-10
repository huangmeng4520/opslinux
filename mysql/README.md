## 修改密码
```
/usr/bin/mysqladmin -u root password '123456'

三、Mysql赋权限和修改密码

mysql> grant all privileges on *.* to root@"%" identified by '123456';
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> show grants for root@"%";
+-------------------------------------------------------------+
| Grants for root@%                                           |
+-------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION |
+-------------------------------------------------------------+
1 row in set (0.00 sec)
```
