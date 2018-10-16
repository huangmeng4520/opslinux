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

