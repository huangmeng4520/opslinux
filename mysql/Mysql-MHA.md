## 测试服务器环境均为Centos7，测试结构如下图：

  ![MHA测试架构](https://github.com/Lancger/opslinux/blob/master/images/MHA测试架构.png)
  
  其中master对外提供写服务，备选master（实际的slave，主机名server03）提供读服务，slave也提供相关的读服务，一旦master宕机，将会把备选master提升为新的master，slave指向新的master
  
```
mysql-master：      192.168.56.10            ------> mha_node1 

mysql-master-bak：  192.168.56.20            ------> mha_node2

mysql-slave：       192.168.56.30            ------> mha_manager
```

## 环境部署：
```
三台机器安装mysql5.6，并配置好主从

分别在mysql中执行grant all on *.* to root@'%' identified by '123456';
````
