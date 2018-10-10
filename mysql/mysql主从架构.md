## 一、MySQL主从介绍

    1）MySQL主从又叫做Replication、AB复制。简单讲就是A和B两台机器做主从后，在A上写数据，另外一台B也会跟着写数据，两者数据实时同步的

    2）MySQL主从是基于binlog的，主上须开启binlog才能进行主从。

## 二、MySQL主从原理图如下：

  ![Mysql主从原理图](https://github.com/Lancger/opslinux/blob/master/images/mysql-ab.png)
  
  ##### 复制方式

    MySQL5.6开始主从复制有两种方式：基于日志（binlog）、基于GTID（全局事务标示符）。 
    本文只涉及基于日志binlog的主从配置
    
    复制原理,主从过程大致有3个步骤
    1、Master将数据改变记录到二进制日志(binary log)中，也就是配置文件log-bin指定的文件，这些记录叫做二进制日志事件(binary log events) 
    2、Slave通过I/O线程读取Master中的binary log events并写入到它的中继日志(relay log) 
    3、Slave重做中继日志中的事件，把中继日志中的事件信息按顺序一条一条的在本地执行一次，完成数据在本地的存储，从而实现将改变反映到它自己的数据(数据重放)
    
    #### a、主上有一个log dump线程，用来和从的I/O线程传递binlog

    #### b、从上有两个线程，其中I/O线程用来同步主的binlog并生成relaylog，另外一个SQL线程用来把relaylog里面的sql语句落地

    其中binlog  二进制日志

    relaylog  中继日志
    
 ##### 要求

    1、主从服务器操作系统版本和位数一致 
    2、Master和Slave数据库的版本要一致 
    3、Master和Slave数据库中的数据要一致 
    4、Master开启二进制日志，Master和Slave的server_id在局域网内必须唯一

## 三、MySQL主从配置使用场景

    1、将从用于做数据备份

    2、从不仅用于数据备份，而且还用于web客户端读取从上的数据，减轻主读的压力
