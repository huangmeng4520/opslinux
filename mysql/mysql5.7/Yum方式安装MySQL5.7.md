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
   
    首先启动MySQL
   
    systemctl start  mysqld.service
    
    查看MySQL运行状态，运行状态如图：
   
    此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：
   
    grep "password" /var/log/mysqld.log
