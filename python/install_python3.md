## 一、安装依赖
```bash
yum install -y readline readline-devel gcc gcc-c++ zlib zlib-devel openssl openssl-devel sqlite-devel python-devel

#python3.7版本需要
yum install libffi-devel -y
```

## 二、下载并安装python3.6
```bash
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
tar -xzf Python-3.7.0.tgz 
cd Python-3.7.0
./configure --prefix=/usr/local/python3.7 --enable-shared
make && make install
ln -s /usr/local/python3.7/bin/python3.7 /usr/bin/python3
ln -s /usr/local/python3.7/bin/pip3 /usr/bin/pip3
ln -s /usr/local/python3.7/bin/pyvenv /usr/bin/pyvenv

# 链接库文件
cp /usr/local/python3.7/lib/libpython3.6m.so.1.0 /usr/local/lib
cd /usr/local/lib
ln -s libpython3.7m.so.1.0 libpython3.7m.so
echo '/usr/local/lib' >> /etc/ld.so.conf
/sbin/ldconfig
```
