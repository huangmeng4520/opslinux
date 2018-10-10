## 一、设置主机host
```
cat > /etc/hosts <<EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.56.10 master master.example.com
192.168.56.20 node1  node1.example.com
192.168.56.30 node2  node2.example.com
EOF
```

## 二、设置yum源
```
#Centos自带的repo是不会自动更新每个软件的最新版本，所以无法通过yum方式安装MySQL的高级版本。所以，即使使劲用yum -y install mysql mysql-server mysql-devel，也是没用的。 所以，正确的安装mysql5姿势是要先安装带有可用的mysql5系列社区版资源的rpm包

rpm -Uvh http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

#这个时候查看当前可用的mysql安装资源：

yum repolist enabled | grep "mysql.*-community.*"
```

## 三、通过 Yum 来安装 MySQL
```
yum clean all   
yum -y install mysql-community-server
```

## 四、启动mysql服务

```
systemctl enable mysqld
systemctl start mysqld
mysql_secure_installation
```

## 五、mysql安全设置（设置密码）
```
mysql_secure_installation
```

## 六、设置字符集
```
修改 /etc/my.cnf 文件，添加字符集的设置

[mysqld]
character_set_server = utf8

[mysql]
default-character-set = utf8

重启 MySQL ,可以看到字符集已经修改了
```
