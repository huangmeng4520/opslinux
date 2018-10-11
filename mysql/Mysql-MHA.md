## 一、结构图

    #测试服务器环境均为Centos7，测试结构如下图：

  ![MHA测试架构](https://github.com/Lancger/opslinux/blob/master/images/MHA测试架构.png)
  
    其中master对外提供写服务，备选master-bak提供读服务，slave也提供相关的读服务，一旦master宕机，将会把备选master-bak提升为新的master，slave指向新的master
  
```
mysql-master：      192.168.56.10            ------> mha_node1 

mysql-master-bak：  192.168.56.20            ------> mha_node2

mysql-slave：       192.168.56.30            ------> mha_manager
```

## 环境部署：

    三台机器安装mysql5.6，并配置好主从

```

1、更改主机名，添加hosts文件
hostnamectl set-hostname mha_node1
hostnamectl set-hostname mha_node2
hostnamectl set-hostname mha_manager

cat > /etc/hosts <<EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.56.10 mha_node1   mha_node1.example.com
192.168.56.20 mha_node2   mha_node1.example.com
192.168.56.30 mha_manager mha_manager.example.com
EOF

1、安装依赖包
yum install -y gcc ntpdate wget lrzsz vim net-tools openssh-clients*

2、安装epel源
yum install -y epel-release

3、安装组件

````
