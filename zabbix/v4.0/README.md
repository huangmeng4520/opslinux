# ZABBIX 4.0 LTS+Grafana5.3部署

## 一、概述
### 1、Zabbix 4.0 LTS
    2018年10月1日，Zabbix官方正式发布Zabbix 4.0 LTS版本，作为长期支持版本，意味着可以获得官方5年的支持。其中完全支持到2021年10月31日，以及有限支持到2023年10月31日，同时官方4.0文档已经更新。
    最直观的感受就是重新设计了图形展示，新增了Kiosk模式实现真正意义上的全屏，可以直接做大屏展示，时间选择器做的和Kibana类似；
    Zabbix 4.0 LTS对分布式监控Proxy方式也做了优化，引入了与Proxy通信的压缩，大大减少了传输数据的大小。从而提高了性能。

    Zabbix 4.0 LTS 详细了解优化及新增功能参考如下:
    新增功能:https://www.zabbix.com/whats_new
    官方文档:https://www.zabbix.com/documentation/4.0/manual

### 2、Grafana5.3

    Grafana v5.3带来了新功能，许多增强功能和错误修复。
    Google Stackdriver作为核心数据源;
    电视模式得到改善，更易于访问
    提醒通知提醒;
    Postgres获得了一个新的查询构建器;
    改进了对Gitlab的OAuth支持;
    带模板变量过滤的注释;
    具有自由文本支持的变量。

    Grafana5.3 详细了解优化及新增功能参考如下:
    新增功能:http://docs.grafana.org/guides/whats-new-in-v5-3/
    
### 3、部署环境准备
    操作系统: CentOS Linux release 7.5.1804 (Core) 
    软件版本: zabbix-release-4.0-1.el7.noarch.rpm
    数据库: mysql 5.6.41
    grafana版本: grafana-5.3.0-1.x86_64.rpm

## 二、安装及配置 Zabbix server

### 1. Install Repository with MySQL database
      mkdir /app/tools -p && cd /app/tools
      wget https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-1.el7.noarch.rpm
      yum -y install zabbix-release-4.0-1.el7.noarch.rpm

### 2. 安装Zabbix server, frontend, agent
      yum -y install zabbix-server-mysql zabbix-web-mysql zabbix-agent

### 3. mysql5.6安装及配置数据库

    centos自带的repo是不会自动更新每个软件的最新版本，所以无法通过yum方式安装MySQL的高级版本。
    安装mysql5姿势是要先安装带有可用的mysql5系列社区版资源的rpm包
    wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
    yum -y install mysql-community-release-el7-5.noarch.rpm

    查看当前可用的mysql安装资源
    yum list
    yum repolist enabled | grep "mysql.*-community.*"

    mysql-connectors-community/x86_64 MySQL Connectors Community                  65
    mysql-tools-community/x86_64      MySQL Tools Community                       69
    mysql56-community/x86_64          MySQL 5.6 Community Server                 412

    使用yum的方式安装MySQL
    yum -y install mysql-community-server

    启动mysql并设置开机启动
    systemctl enable mysqld
    systemctl start mysqld

    mysql -uroot -p
    password
    mysql> create database zabbix character set utf8 collate utf8_bin;
    mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
    mysql> quit;

    将zabbix数据表导入数据库中
    zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix

### 4.配置数据库zabbix server
    vim /etc/zabbix/zabbix_server.conf 
    DBPassword=zabbix

### 5.编辑Zabbix前端PHP配置,更改时区
    vim /etc/httpd/conf.d/zabbix.conf
    php_value date.timezone Asia/Shanghai

### 6.启动zabbix-server zabbix-agent httpd 并设置开机启动
    systemctl restart zabbix-server zabbix-agent httpd
    systemctl enable zabbix-server zabbix-agent httpd

    http://172.16.8.69/zabbix/setup.php
