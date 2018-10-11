## 一、告警现象

  ![iotop抓取图](https://github.com/Lancger/opslinux/blob/master/images/iotop.jpg)
  ![iostat抓取图](https://github.com/Lancger/opslinux/blob/master/images/iostat.jpg)

## 二、现场抓取
```
#
[root@tw06a1671 ~]# iotop
Total DISK READ: 0.00 B/s | Total DISK WRITE: 41.00 K/s
  TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN     IO>    COMMAND                                                                                                              
52026 be/4 root        0.00 B/s    3.42 K/s  0.00 %  0.00 % java -Xms256m -Xmx256m -server -XX:+UseConcMarkSweepGC -XX:~-client/.o_galaxy_xyipk.exist --skip 86400 --ignore_ts false
33857 be/4 nobody      0.00 B/s  293.83 K/s  0.00 %  0.00 % nginx: worker process
33858 be/4 nobody      0.00 B/s  201.58 K/s  0.00 %  0.00 % nginx: worker process
33859 be/4 nobody      0.00 B/s  205.00 K/s  0.00 %  0.00 % nginx: worker process
33860 be/4 nobody      0.00 B/s  150.33 K/s  0.00 %  0.00 % nginx: worker process
33865 be/4 nobody      0.00 B/s  245.99 K/s  0.00 %  0.00 % nginx: worker process
33866 be/4 nobody      0.00 B/s  208.41 K/s  0.00 %  0.00 % nginx: worker process

#iostat -x -k -d 1 2。每隔2S输出磁盘IO的详细详细，总共采样5次。
[root@tw06a1671 ~]# iostat -x 2 5
Linux 2.6.32-431.el6.x86_64 (tw06a1671)         10/11/2018      _x86_64_        (24 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           9.76    0.00    7.81    0.18    0.00   82.25

Device:         rrqm/s   wrqm/s     r/s     w/s   rsec/s   wsec/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
sdb               0.00   959.11    0.41   23.22    70.35  7858.67   335.48     0.12    5.25   17.44    5.04   2.12   5.01
sda               0.77     6.42    0.62    8.45    15.57   118.94    14.83     0.04    4.49    9.18    4.14   3.09   2.80


rrqm/s: 每秒对该设备的读请求被合并次数，文件系统会对读取同块(block)的请求进行合并
wrqm/s: 每秒对该设备的写请求被合并次数
r/s: 每秒完成的读次数
w/s: 每秒完成的写次数
rkB/s: 每秒读数据量(kB为单位)
wkB/s: 每秒写数据量(kB为单位)
avgrq-sz:平均每次IO操作的数据量(扇区数为单位)
avgqu-sz: 平均等待处理的IO请求队列长度
await: 平均每次IO请求等待时间(包括等待时间和处理时间，毫秒为单位)
svctm: 平均每次IO请求的处理时间(毫秒为单位)
%util: 采用周期内用于IO操作的时间比率，即IO队列非空的时间比率


重点关注参数

1、iowait% 表示CPU等待IO时间占整个CPU周期的百分比，如果iowait值超过50%，或者明显大于%system、%user以及%idle，表示IO可能存在问题。

2、avgqu-sz 表示磁盘IO队列长度，即IO等待个数。

3、await 表示每次IO请求等待时间，包括等待时间和处理时间

4、svctm 表示每次IO请求处理的时间

5、%util 表示磁盘忙碌情况，一般该值超过80%表示该磁盘可能处于繁忙状态。
```

## 三、分析
