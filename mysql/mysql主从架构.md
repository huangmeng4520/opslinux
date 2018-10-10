## 一、MySQL主从介绍

. MySQL主从又叫做Replication、AB复制。简单讲就是A和B两台机器做主从后，在A上写数据，另外一台B也会跟着写数据，两者数据实时同步的

. MySQL主从是基于binlog的，主上须开启binlog才能进行主从。

### 主从过程大致有3个步骤

1）主将更改操作记录到binlog里

2）从将主的binlog事件(sql语句)同步到从本机上并记录在relaylog里

3）从根据relaylog里面的sql语句按顺序执行

• 主上有一个log dump线程，用来和从的I/O线程传递binlog

• 从上有两个线程，其中I/O线程用来同步主的binlog并生成relaylog，另外一个SQL线程用来把relaylog里面的sql语句落地

    其中binlog  二进制日志

    relaylog  中继日志

MySQL主从原理图如下：

    MySQL主从配置使用场景

    1）将从用于做数据备份

    2）从不仅用于数据备份，而且还用于web客户端读取从上的数据，减轻主读的压力
