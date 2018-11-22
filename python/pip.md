 ## 一、安装pip
    
    wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py
    python get-pip.py

    pip install --trusted-host mirrors.aliyun.com -i https://mirrors.aliyun.com/pypi/simple/  -r requirements.txt

    pip install -i https://pypi.mirrors.ustc.edu.cn/simple/  -r requirements.txt       可用的

    pip install -i https://pypi.douban.com/simple/  -r requirements.txt       可用的

    
    遇到SSL错误可使用下面方式
    pip install -r requirements.txt -i http://pypi.douban.com/simple --trusted-host pypi.douban.com 
