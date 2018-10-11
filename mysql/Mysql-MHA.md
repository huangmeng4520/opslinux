## 一、结构图

    #测试服务器环境均为Centos7，测试结构如下图：

  ![MHA测试架构](https://github.com/Lancger/opslinux/blob/master/images/MHA测试架构.png)
  
    其中master对外提供写服务，备选master-bak提供读服务，slave也提供相关的读服务，一旦master宕机，将会把备选master-bak提升为新的master，slave指向新的master
  
```
mysql-master：      192.168.56.10            ------> mha_node1 

mysql-master-bak：  192.168.56.20            ------> mha_node2

mysql-slave：       192.168.56.30            ------> mha_manager
```

## 二、环境初始化

    三台机器安装mysql5.6，并配置好主从

```
1、关闭防火墙以及selinx
systemctl disable firewalld 
systemctl disable NetworkManager

sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

setenforce 0 

2、集群主机时间同步
yum install -y wget lrzsz vim net-tools openssh-clients ntpdate unzip xz telnet

#加入crontab
1 * * * * /usr/sbin/ntpdate ntp1.aliyun.com >/dev/null 2>&1

#设置时区
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

3、更改主机名，添加hosts文件
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

4、配置密钥ssh(所有主机各配置相同操作)
ssh-keygen -t rsa 

在所有主机都必须拷贝密钥于其他主机
for i in mha_node1 mha_node2 mha_manager; do ssh-copy-id $i; done

测试登录
for i in mha_node1 mha_node2 mha_manager; do ssh $i; done

```

## 三、环境初始化
```
1、安装epel源
yum install -y epel-release

wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm

2、安装依赖包
yum clean all
yum -y install perl-DBD-MySQL perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager perl-Config-IniFiles ncftp perl-Params-Validate perl-CPAN perl-Test-Mock-LWP.noarch perl-LWP-Authen-Negotiate.noarch perl-devel perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker

````

## 四、Master节点
    1、仅在manager节点上安装mha管理软件
```
wget https://qiniu.wsfnk.com/mha4mysql-manager-0.58-0.el7.centos.noarch.rpm
#wget https://github.com/yoshinorim/mha4mysql-manager/releases/download/v0.58/mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

#安装
rpm -ivh mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

#卸载
rpm -e mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

```
#### 2、配置MHA（在manager节点上操作）
```
```
 
## 五、Node节点
    1、在三个节点上都装mha的node软件
```
wget https://qiniu.wsfnk.com/mha4mysql-node-0.58-0.el7.centos.noarch.rpm
#wget https://github.com/yoshinorim/mha4mysql-node/releases/download/v0.58/mha4mysql-node-0.58-0.el7.centos.noarch.rpm

#安装	
rpm -ivh mha4mysql-node-0.58-0.el7.centos.noarch.rpm

#卸载
rpm -e mha4mysql-manager-0.58-0.el7.centos.noarch.rpm

```

### 六、查看软件包的安装位置

```
#查看包的安装位置
root># rpm -qa|grep mha
mha4mysql-node-0.58-0.el7.centos.noarch
mha4mysql-manager-0.58-0.el7.centos.noarch

root># rpm -ql mha4mysql-manager-0.58-0.el7.centos.noarch
/usr/bin/masterha_check_repl
/usr/bin/masterha_check_ssh
/usr/bin/masterha_check_status
/usr/bin/masterha_conf_host

root># rpm -ql mha4mysql-node-0.58-0.el7.centos.noarch
/usr/bin/apply_diff_relay_logs
/usr/bin/filter_mysqlbinlog
/usr/bin/purge_relay_logs

#卸载
yum remove -y mha4mysql-manager-0.58-0.el7.centos.noarch
yum remove -y mha4mysql-node-0.58-0.el7.centos.noarch
```
