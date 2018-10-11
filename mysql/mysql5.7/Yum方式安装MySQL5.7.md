在CentOS中默认安装有MariaDB，这个是MySQL的分支，但为了需要，还是要在系统中安装MySQL，而且安装完成之后可以直接覆盖掉MariaDB。

## 1、下载并安装MySQL官方的 Yum Repository
```
yum list installed | grep mysql
yum -y remove mysql-libs.x86_64

wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql-community-server
```

## 2、MySQL数据库设置
   
    1、首先启动MySQL
   
    systemctl start mysqld.service
    
    2、查看MySQL运行状态，运行状态如图：
    
    systemctl status mysqld.service

    3、此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：
    
    grep "password" /var/log/mysqld.log

    4、如下命令进入数据库
    
    mysql -uroot -p
      
    5、输入初始密码，此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库：
    
    ALTER USER 'root'@'localhost' IDENTIFIED BY '1Qaz2Wsx3Edc!@#';
    
    这里有个问题，新密码设置的时候如果设置的过于简单会报错：
    
    原因是因为MySQL有密码设置的规范，具体是与validate_password_policy的值有关：
    
    6、MySQL完整的初始密码规则可以通过如下命令查看：
    
      mysql> SHOW VARIABLES LIKE 'validate_password%';
      +--------------------------------------+-------+
      | Variable_name                        | Value |
      +--------------------------------------+-------+
      | validate_password_check_user_name    | OFF   |
      | validate_password_dictionary_file    |       |
      | validate_password_length             | 4     |
      | validate_password_mixed_case_count   | 1     |
      | validate_password_number_count       | 1     |
      | validate_password_policy             | LOW   |
      | validate_password_special_char_count | 1     |
      +--------------------------------------+-------+
      7 rows in set (0.01 sec)

    7、修改密码长度和策略限制
    
    mysql> set global validate_password_policy=0;
    mysql> set global validate_password_length=1;
      
    8、但此时还有一个问题，就是因为安装了Yum Repository，以后每次yum操作都会自动更新，需要把这个卸载掉：
     
    yum -y remove mysql57-community-release-el7-10.noarch
     
    大功告成
      
      
