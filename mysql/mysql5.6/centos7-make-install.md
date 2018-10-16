## 一、安装依赖包
```bash
yum install -y gcc gcc-c++ ncurses-devel perl cmake autoconf 
```

## 二、设置MySQL组和用户
```bash
groupadd mysql
useradd -M -s /sbin/nologin -r -g mysql mysql    
#useradd -r -g mysql mysql
#passwd mysql
```

## 三、创建所需要的目录
```bash
#新建mysql安装目录
mkdir  -p /usr/local/mysql/

#新建mysql数据库数据文件目录
mkdir  -p /data/mysql/
```

## 四、下载MySQL源码包并解压
```bash
wget https://cdn.mysql.com//Downloads/MySQL-5.6/mysql-5.6.39.tar.gz
```

## 五、编译安装
```bash
cmake \
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DMYSQL_DATADIR=/data/mysql/ \
-DMYSQL_TCP_PORT=3306 \
-DENABLE_DOWNLOADS=0
```
    参数解释
 ![mysql5.6编译参数详解](https://github.com/Lancger/opslinux/blob/master/images/mysql5.6-make.png)
    
    注：重新运行配置，需要删除CMakeCache.txt文件

```bash
rm CMakeCache.txt
```
```bash
#编译源码
make

#安装
make install
```
## 六、初始化mysql数据库
```bash
touch /usr/local/mysql/mysql.error

chown -R mysql.mysql /usr/local/mysql

cd /usr/local/mysql/scripts

./mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql
```
    #复制mysql服务启动配置文件
    cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf
    
    #复制mysql服务启动脚本及加入PATH路径
    cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld 

    vim /etc/profile
    export  PATH=/usr/local/mysql/bin:/usr/local/mysql/lib:$PATH

    source /etc/profile

## 七、配置my.cnf
```bash
[client]
port            = 3306
socket          = /usr/local/mysql/mysql.sock   
default-character-set=utf8    
 
[mysqld]    
port            = 3306
socket          = /usr/local/mysql/mysql.sock
pid-file        = /usr/local/mysql/mysql.pid
character_set_server=utf8   
character_set_client=utf8
collation-server=utf8_general_ci
log-error      = /usr/local/mysql/mysql.sock



#(注意linux下mysql安装完后是默认：表名区分大小写，列名不区分大小写； 0：区分大小写，1：不区分大小写)    
lower_case_table_names=1

#(设置最大连接数，默认为 151，MySQL服务器允许的最大连接数16384; )    
max_connections=1000
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
 
[mysql]
default-character-set = utf8
```
