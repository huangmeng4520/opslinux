## 测试服务器环境均为Centos7，测试结构如下图：

  ![MHA测试架构](https://github.com/Lancger/opslinux/blob/master/images/MHA测试架构.png)
```
mysql-master：      192.168.56.10            ------> mha_node1 

mysql-master-bak：  192.168.56.20            ------> mha_node2

mysql-slave：       192.168.56.30            ------> mha_manager
```
