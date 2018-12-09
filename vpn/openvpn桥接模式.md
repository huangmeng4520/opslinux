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
rm -f 3 3.0
cd 3.0.3/
find / -type f -name "vars.example" | xargs -i cp {} . && mv vars.example vars
```
2. 生成证书
创建一个新的PKI和CA
```
[root@localhost 3.0.3]# pwd
/etc/openvpn/easy-rsa/3.0.3
[root@localhost 3.0.3]# ./easyrsa init-pki  ------------------#创建空的pki

Note: using Easy-RSA configuration from: ./vars

init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /etc/openvpn/easy-rsa/3.0.3/pki

[root@localhost 3.0.3]# ./easyrsa build-ca nopass ----------------#创建新的CA，不使用密码

Note: using Easy-RSA configuration from: ./vars
Generating a 2048 bit RSA private key
......................+++
................................................+++
writing new private key to '/etc/openvpn/easy-rsa/3.0.3/pki/private/ca.key.pClvaQ1GLD'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [Easy-RSA CA]: ------------回车

CA creation complete and you may now import and sign cert requests.
Your new CA certificate file for publishing is at:
/etc/openvpn/easy-rsa/3.0.3/pki/ca.crt
```
3. 创建服务端证书
```
[root@localhost 3.0.3]# ./easyrsa gen-req server nopass

Note: using Easy-RSA configuration from: ./vars
Generating a 2048 bit RSA private key
...........................+++
..............................................................................+++
writing new private key to '/etc/openvpn/easy-rsa/3.0.3/pki/private/server.key.wy7Q0fuG6A'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [server]: 回车

Keypair and certificate request completed. Your files are:
req: /etc/openvpn/easy-rsa/3.0.3/pki/reqs/server.req
key: /etc/openvpn/easy-rsa/3.0.3/pki/private/server.key
```
 参考文档：
 
 https://blog.rj-bai.com/post/132.html#menu_index_11
