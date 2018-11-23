```
yum -y install libcgroup gcc libcap-devel 

```
## 一、配置文件/etc/cgconfig.conf

```
[root@localhost ~]# cat /etc/cgconfig.conf
#
#  Copyright IBM Corporation. 2007
#
#  Authors:	Balbir Singh <balbir@linux.vnet.ibm.com>
#  This program is free software; you can redistribute it and/or modify it
#  under the terms of version 2.1 of the GNU Lesser General Public License
#  as published by the Free Software Foundation.
#
#  This program is distributed in the hope that it would be useful, but
#  WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
#
# See man cgconfig.conf for further details.
#
# By default, mount all controllers to /cgroup/<controller>

mount {
	cpuset	= /cgroup/cpuset;
	cpu	= /cgroup/cpu;
	cpuacct	= /cgroup/cpuacct;
	memory	= /cgroup/memory;
	devices	= /cgroup/devices;
	freezer	= /cgroup/freezer;
	net_cls	= /cgroup/net_cls;
	blkio	= /cgroup/blkio;
}

group io-test {
    perm {
          task{
              uid=www;
              gid=www;
          }

          admin{
             uid=root;
             gid=root;
          }

    } blkio {
        blkio.throttle.write_iops_device="";
        blkio.throttle.read_iops_device="";
        blkio.throttle.write_bps_device="8:0 1048576";
        blkio.throttle.read_bps_device="8:0 1048576";
        blkio.reset_stats="";
        blkio.weight="500";
        blkio.weight_device="";
    }
}
```
## 二、配置文件/etc/cgrules.conf
```
[root@localhost ~]# cat /etc/cgrules.conf
# /etc/cgrules.conf
#The format of this file is described in cgrules.conf(5)
#manual page.
#
# Example:
#<user>		<controllers>	<destination>
#@student	cpu,memory	usergroup/student/
#peter		cpu		test1/
#%		memory		test2/
*:       blkio            io-test
# End of file
```

chkconfig cgred on
chkconfig cgconfig on

service cgred restart
service cgconfig restart

```


参考文档：


https://stackoverflow.com/questions/24959846/cgroup-blkio-files-cannot-be-written     限制IO的参考


https://www.cnblogs.com/yanghuahui/p/3751826.html

https://www.jianshu.com/p/dc3140699e79


https://blog.csdn.net/lanyang123456/article/details/82319911
