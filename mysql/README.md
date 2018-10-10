## 一、修改密码
```
#方式一
/usr/bin/mysqladmin -u root password '123456'

#方式二
mysql> set password=password('123456');

#方式三
mysql> use mysql
mysql> desc user;
mysql> GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "root";
mysql> update user set Password = password('123456') where User='root';
mysql> show grants for root@"%";
mysql> flush privileges;
mysql> select Host,User,Password from user where User='root';
mysql> exit
```

## 一、插入数据
