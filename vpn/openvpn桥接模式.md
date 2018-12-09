 # 一、Centos7.4 搭建 openvpn

    openvpn有两种模式，桥接模式和路由模式：

    桥接模式相当于网关模式，只需要内网一台服务器做server段，那么客户端就可以通过server访问内网的所有服务器；

    路由模式则是一对一的关系，客户端只能访问安装了vpn server段的服务器，这里讲的是openvpn桥接模式的搭建

1. 添加yum源
```shell
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum makecache
``` 
2. 安装 openvpn
```shell
yum -y install openvpn easy-rsa
```

# 二、配置easy-rsa-3.0
1. 复制文件
```
cp -r /usr/share/easy-rsa/ /etc/openvpn/easy-rsa
cd /etc/openvpn/easy-rsa/
rm -y 3 3.0
cd 3.0.3/
find / -type f -name "vars.example" | xargs -i cp {} . && mv vars.example vars
```

 参考文档：
 
 https://blog.rj-bai.com/post/132.html#menu_index_11
