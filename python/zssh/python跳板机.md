    yum install mysql-devel
    yum install mysql-python
    
    
    wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py
    python get-pip.py

    pip install -i https://pypi.douban.com/simple/ mysql-python

    create database sa;
    use sa;
    create table password (ip varchar(15) primary key not null, muser varchar(15), mpass varchar(30), rpass varchar(30));

    mysql -uroot -p1Qaz2Wsx3Edc -e 'delete from sa.password where ip="120.79.210.87";'

    [root@ip-172-31-17-178 opt]# python zssh.py 120.79.210.87
    www 登录成功
    root 登录成功

### 参考文档：

https://blog.csdn.net/u012974916/article/details/53316976
