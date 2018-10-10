一、前言：为什么MySQL要做主从复制（读写分离）？
通俗来讲，如果对数据库的读和写都在同一个数据库服务器中操作，业务系统性能会降低。
为了提升业务系统性能，优化用户体验，可以通过做主从复制（读写分离）来减轻主数据库的负载。
而且如果主数据库宕机，可快速将业务系统切换到从数据库上，可避免数据丢失。

二、MySQL主从复制（读写分离）和集群的区别：
我对MySQL也是刚开始研究，不是很专业。我的理解是：
1、主从复制（读写分离）：一般需要两台及以上数据库服务器即可（一台用于写入数据，一台用于同步主的数据并用于数据查询操作）。
局限性：
（1）配置好主从复制之后，同一张表，只能对一个服务器写操作。如果在从上执行了写操作，而之后主也操作了这张表，或导致主从不同步；据说可以配置成主主方式，但我还没有研究到。
（2）主数据库服务器宕机，需要手动将业务系统切换到从数据库服务器。无法做到高可用性（除非再通过部署keepalive做成高可用方案）。
2、集群是由N台数据库服务器组成，数据的写入和查询是随机到任意一台数据库服务器的，其他数据库服务器会自动同步数据库的操作。
任何一台数据库宕机，不会对整个集群造成大的影响。
局限性：我经过测试才知道目前mysql集群版本（MySQL Cluster）只能对NDB存储引擎的数据进行集群同步，如果是INNODB或其他的MySQL存储引擎是不行的。这个也导致了我放弃了在业务系统中应用这种方案。

三、回归正题，接下来开始MySQL5.6.12的主从复制教程：
1、MySQL5.6开始主从复制有两种方式：基于日志（binlog）；基于GTID（全局事务标示符）。
需要注意的是：GTID方式不支持临时表！所以如果你的业务系统要用到临时表的话就不要考虑这种方式了，至少目前最新版本MySQL5.6.12的GTID复制还是不支持临时表的。
所以此篇教程主要是告诉大家如何通过日志（binlog）方式做主从复制！

2、MySQL官方提供的MySQL Replication教程：
http://dev.mysql.com/doc/refman/5.6/en/replication.html
这个官方教程强烈建议大家阅读（需要一定的英语阅读能力哦！不行就google翻译后再阅读吧~）。

3、准备工作：
（1）配置MySQL主从复制（读写分离）之前，需要在主从两台服务器先安装好MySQL5.6。
（2）目前最新的MySQL5.6 GA版本是MySQL5.6.12（点此下载MySQL5.6.12源码包）。
个人推荐Linux（RedHat/CentOS 6.4）源码编译安装，具体可以看本站这篇教程：RedHat/CentOS源码编译安装MySQL5.6.12
（3）注意：
（a）如果你需要用于生产环境，安教程安装MySQL时不要急着做mysql启动操作。建议把mysql初始化生成的/usr/local/mysql/mysql.cnf删除，然后把你优化好的mysql配置文件my.cnf放到/etc下。
（b）建议主备两台服务器在同一局域网，主备两台数据库网络需要互通。
（4）我的环境：
主数据库IP：192.168.100.2
从数据库IP：192.168.100.3

4、修改主数据库的的配置文件：
1     [mysqld]
2     server-id=1
3     log-bin=mysqlmaster-bin.log
4     sync_binlog=1
5     #注意：下面这个参数需要修改为服务器内存的70%左右
6     innodb_buffer_pool_size = 512M
7     innodb_flush_log_at_trx_commit=1
8     sql_mode=STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_AUTO_VALUE_ON_ZERO
9     lower_case_table_names=1
10     log_bin_trust_function_creators=1

修改之后要重启mysql：
1     # /etc/init.d/mysql restart

附一个我已优化过的主数据库配置文件：点此下载

5、修改从数据库的的配置文件（server-id配置为大于1的数字即可）：
1     [mysqld]
2     server-id=2
3     log-bin=mysqlslave-bin.log
4     sync_binlog=1
5     #注意：下面这个参数需要修改为服务器内存的70%左右
6     innodb_buffer_pool_size = 512M
7     innodb_flush_log_at_trx_commit=1
8     sql_mode=STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,NO_AUTO_VALUE_ON_ZERO
9     lower_case_table_names=1
10     log_bin_trust_function_creators=1

修改之后要重启mysql：
1     # /etc/init.d/mysql restart

附一个我已优化过的从数据库配置文件：点此下载

6、SSH登录到主数据库：
（1）在主数据库上创建用于主从复制的账户（192.168.100.3换成你的从数据库IP）：
1     # mysql -uroot -p
2     mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.100.3' IDENTIFIED BY 'repl';

（2）主数据库锁表（禁止再插入数据以获取主数据库的的二进制日志坐标）：
1     mysql> FLUSH TABLES WITH READ LOCK;

（3）然后克隆一个SSH会话窗口，在这个窗口打开MySQL命令行：
1     # mysql -uroot -p
2     mysql> SHOW MASTER STATUS;
3     +------------------------+----------+--------------+------------------+-------------------+
4     | File                   | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
5     +------------------------+----------+--------------+------------------+-------------------+
6     | mysqlmaster-bin.000001 |      332 |              |                  |                   |
7     +------------------------+----------+--------------+------------------+-------------------+
8     1 row in set (0.00 sec)
9     mysql> exit;

在这个例子中，二进制日志文件是mysqlmaster-bin.000001，位置是332，记录下这两个值，稍后要用到。
（4）在主数据库上使用mysqldump命令创建一个数据快照：
1     #mysqldump -uroot -p -h127.0.0.1 -P3306 --all-databases  --triggers --routines --events >all.sql
2     # 接下来会提示你输入mysql数据库的root密码，输入完成后，如果当前数据库不大，很快就能导出完成。

（5）解锁第（2）步主数据的锁表操作：
1     mysql> UNLOCK TABLES;

7、SSH登录到从数据库：
（1）通过FTP、SFTP或其他方式，将上一步备份的主数据库快照all.sql上传到从数据库某个路径，例如我放在了/home/yimiju/目录下；
（2）从导入主的快照：
1     # cd /home/yimiju
2     # mysql -uroot -p -h127.0.0.1 -P3306 < all.sql
3     # 接下来会提示你输入mysql数据库的root密码，输入完成后，如果当前数据库不大，很快就能导入完成。

（3）给从数据库设置复制的主数据库信息（注意修改MASTER_LOG_FILE和MASTER_LOG_POS的值）：
1     # mysql -uroot -p
2     mysql> CHANGE MASTER TO MASTER_HOST='192.168.100.2',MASTER_USER='repl',MASTER_PASSWORD='repl',MASTER_LOG_FILE='mysqlmaster-bin.000001',MASTER_LOG_POS=332;
3     # 然后启动从数据库的复制线程：
4     mysql> START slave;
5     # 接着查询数据库的slave状态：
6     mysql>  SHOW slave STATUS \G
7     # 如果下面两个参数都是Yes，则说明主从配置成功！
8     Slave_IO_Running: Yes
9     Slave_SQL_Running: Yes

（4）接下来你可以在主数据库上创建数据库、表、插入数据，然后看从数据库是否同步了这些操作。
