## 一、下载并安装python3.6
```bash
wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
tar -xzf Python-3.6.6.tgz 
cd Python-3.6.6
./configure --prefix=/usr/local/python3.6 --enable-shared
make && make install
ln -s /usr/local/python3.6/bin/python3.6 /usr/bin/python3
ln -s /usr/local/python3.6/bin/pip3 /usr/bin/pip3
ln -s /usr/local/python3.6/bin/pyvenv /usr/bin/pyvenv

# 链接库文件
cp /usr/local/python3.6/lib/libpython3.6m.so.1.0 /usr/local/lib
cd /usr/local/lib
ln -s libpython3.6m.so.1.0 libpython3.6m.so
echo '/usr/local/lib' >> /etc/ld.so.conf
/sbin/ldconfig
```
