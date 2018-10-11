## 一、选择Percona Server、MariaDB还是MySQL

1、Mysql三种存储引擎

MySQL提供了两种存储引擎：MyISAM和 InnoDB，MySQL4和5使用默认的MyISAM存储引擎。从MYSQL5.5开始，MySQL已将默认存储引擎从MyISAM更改为InnoDB。

MyISAM没有提供事务支持，而InnoDB提供了事务支持。

XtraDB是InnoDB存储引擎的增强版本，被设计用来更好的使用更新计算机硬件系统的性能，同时还包含有一些在高性能环境下的新特性。

2、Percona  Server分支

Percona Server由领先的MySQL咨询公司Percona发布。

Percona Server是一款独立的数据库产品，其可以完全与MySQL兼容，可以在不更改代码的情况了下将存储引擎更换成XtraDB。是最接近官方MySQL Enterprise发行版的版本。

Percona提供了高性能XtraDB引擎，还提供PXC高可用解决方案，并且附带了percona-toolkit等DBA管理工具箱，

3、MariaDB

MariaDB由MySQL的创始人开发，MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

MariaDB提供了MySQL提供的标准存储引擎，即MyISAM和InnoDB，10.0.9版起使用XtraDB（名称代号为Aria）来代替MySQL的InnoDB。

4、如何选择

综合多年使用经验和性能对比，首选Percona分支，其次是MariaDB，如果你不想冒一点风险，那就选择MYSQL官方版本。
