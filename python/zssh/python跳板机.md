create database sa;
use sa;
create table password (ip varchar(15) primary key not null, muser varchar(15), mpass varchar(30), rpass varchar(30));

mysql -uroot -p1Qaz2Wsx3Edc -e 'delete from sa.password where ip="120.79.210.87";'

python zssh.py 120.79.210.87

### 参考文档：

https://blog.csdn.net/u012974916/article/details/53316976
