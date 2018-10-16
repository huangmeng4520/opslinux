## 一、更新阿里云的yum源
```bash
#下载wget
yum install wget -y

#备份当前的yum源
mv /etc/yum.repos.d /etc/yum.repos.d.backup

#新建空的yum源设置目录
mkdir /etc/yum.repos.d

#下载阿里云的yum源配置
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
## 二、重建缓存
```bash
yum clean all
yum makecache
```
