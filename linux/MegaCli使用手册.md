## 一、下载MegaCli
```
wget ftp://download2.boulder.ibm.com/ecc/sar/CMA/XSA/ibm_utl_sraidmr_megacli-8.00.48_linux_32-64.zip
```
## 二、安装
```
#解压zip安装包
unzip ibm_utl_sraidmr_megacli-8.00.48_linux_32-64.zip

#切换到安装包目录
cd linux/

#使用rpm安装
rpm -ivh Lib_Utils-1.00-09.noarch.rpm MegaCli-8.00.48-1.i386.rpm

#查看文件安装在哪
rpm -ql MegaCli-8.00.48-1.i386

#做软链接
ln -s /opt/MegaRAID/MegaCli/MegaCli64 /bin/MegaCli64
ln -s /opt/MegaRAID/MegaCli/MegaCli64 /sbin/MegaCli64
```

## 三、使用命令及参数
```
MegaCli64 -h
```
