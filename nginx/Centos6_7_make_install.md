## 一、Tengine介绍

　Tengine是由淘宝网发起的Web服务器项目。它在Nginx的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。Tengine的性能和稳定性已经在大型的网站如淘宝网，天猫商城等得到了很好的检验。官方主页 http://tengine.taobao.org/

### 二、Tengine部署

    ###关闭网络管理工具###
    chkconfig NetworkManager off

    ###关闭防火墙###
    /etc/init.d/iptables stop
    chkconfig iptables off

    ###关闭selinux###
    sed -i.bak "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
    sed -i "s/SELINUXTYPE=targeted/SELINUXTYPE=disabled/g" /etc/selinux/config
    setenforce 0

    ###安装ntpdate,保证各服务器间时间一致###
    yum install -y ntpdate wget lrzsz
    
    # 加入crontab
    1 * * * *  (/usr/sbin/ntpdate -s ntp1.aliyun.com;/usr/sbin/hwclock -w) > /dev/null 2>&1
    1 * * * * /usr/sbin/ntpdate -s ntp1.aliyun.com  > /dev/null 2>&1

    ###安装依赖包###
    yum install pcre-devel zlib zlib-devel git -y
    yum install -y gcc gcc-c++ make pcre-devel perl perl-devel git openssh-clients zlib-devel
    #yum install -y gcc gcc-c++ make pcre-devel perl perl-devel git tmux wget curl openssl openssl-devel openldap openldap-devel

    groupadd nginx -g 600                                  #指定www组ID号为600
    useradd -M -s /sbin/nologin -u 600 -r -g nginx nginx   #-u 指定用户ID号 -g 指定用户所属的起始群组 -G指定用户所属的附加群组

    cd /usr/local/src/
    wget https://www.openssl.org/source/old/1.0.2/openssl-1.0.2j.tar.gz
    tar -zxf  /usr/local/src/openssl-1.0.2j.tar.gz

    cd /usr/local/src/
    wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
    tar zxvf LuaJIT-2.0.5.tar.gz
    cd LuaJIT-2.0.5
    make   &&  make install

    cd /usr/local/src/
    git clone https://github.com/simpl/ngx_devel_kit.git

    cd /usr/local/src/
    git clone https://github.com/chaoslawful/lua-nginx-module.git

    cd /usr/local/src/
    wget http://www.kyne.com.au/~mark/software/download/lua-cjson-2.1.0.tar.gz
    tar zxf lua-cjson-2.1.0.tar.gz
    cd lua-cjson-2.1.0
    
    注：
    vim Makefile
    修改：LUA_INCLUDE_DIR =   $(PREFIX)/include/luajit-2.0
    make & make install

    #创建Nginx运行的普通用户
    useradd -s /sbin/nologin -M nginx

    #git clone git://github.com/alibaba/tengine.git;
    #cd  tengine
    cd /usr/local/src/
    wget http://tengine.taobao.org/download/tengine-2.2.2.tar.gz
    tar zxf tengine-2.2.2.tar.gz
    cd tengine-2.2.2

    export LUAJIT_INC=/usr/local/include/luajit-2.0/
    export LUAJIT_LIB=/usr/local/lib
    ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-http_ssl_module --with-openssl-opt="enable-tlsext"  \
                --with-openssl="/usr/local/src/openssl-1.0.2j/" --with-ld-opt="-Wl,-rpath,$LUAJIT_LIB" --add-module=/usr/local/src/ngx_devel_kit \
                --add-module=/usr/local/src/lua-nginx-module --without-http_upstream_check_module --with-http_concat_module --with-http_dav_module \
                --with-http_dyups_module --with-http_dyups_lua_api  --with-http_v2_module --with-http_sysguard_module

    #修改版本信息
    vi src/core/nginx.h

    make && make install

    安装完成，启动nginx服务
    注：如果有以下错误:
    root># service nginx test
    /usr/local/src/nginx/sbin/nginx: error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory

    #报错处理
    ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2   

    解决：
    echo "/usr/local/src/lib" > /etc/ld.so.conf.d/usr_local_lib.conf
    ldconfig


## 三、nginx启动脚本
```
vim /etc/init.d/nginx
```
```
#启动脚本
vim /etc/init.d/nginx
#!/bin/bash
# nginx Startup script for the Nginx HTTP Server
# it is v.1.0 version.
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
#              It has a lot of features, but it's not for everyone.
# processname: nginx
# pidfile: /var/run/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf

nginxd=/usr/local/nginx/sbin/nginx
nginx_config=/usr/local/nginx/conf/nginx.conf
nginx_pid=/var/run/nginx.pid
RETVAL=0
prog="nginx"

# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0
[ -x $nginxd ] || exit 0
# Start nginx daemons functions.
start() {
if [ -e $nginx_pid ];then
   echo "nginx already running...."
   exit 1
fi
   echo -n $"Starting $prog: "
   daemon $nginxd -c ${nginx_config}
   RETVAL=$?
   echo
   [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
   return $RETVAL
}
# Stop nginx daemons functions.
stop() {
        echo -n $"Stopping $prog: "
        killproc $nginxd
        RETVAL=$?
        echo
        [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
}
# reload nginx service functions.
reload() {
    echo -n $"Reloading $prog: "
    #kill -HUP `cat ${nginx_pid}`
    killproc $nginxd -HUP
    RETVAL=$?
    echo
}
# See how we were called.
case "$1" in
start)
        start
        ;;
stop)
        stop
        ;;
reload)
        reload
        ;;
restart)
        stop
        start
        ;;
status)
        status $prog
        RETVAL=$?
        ;;
*)
        echo $"Usage: $prog {start|stop|restart|reload|status|help}"
        exit 1
esac
exit $RETVAL
```
为启动脚本添加执行权限
```
chmod 755 /etc/init.d/nginx
```

![输入图片说明](https://static.oschina.net/uploads/img/201707/07105004_PKgh.png "在这里输入图片标题")

开机自动启动
```
chkconfig nginx on
```

## 四、部署waf

    cd /tmp

    git clone https://github.com/loveshell/ngx_lua_waf

    mv ngx_lua_waf /usr/local/nginx/conf/waf

    https://github.com/loveshell/ngx_lua_waf
    
###编辑nginx配置文件
```
cd /usr/local/nginx/conf/
cp -rf nginx.conf nginx.conf.bak
touch upstream.conf
mkdir vhost
mkdir -p /data0/upload    #nginx配置文件中定义了curl 上传文件的路径
```
```
root># cat nginx.conf
#使用小号
user  www;

#开启进程数
worker_processes  4;

#dso {
#     load ngx_pagespeed.so;
#}
#制定进程到cpu（四cpu：0001 0010 0100 1000）
#worker_cpu_affinity 0001 0010 0100 1000 0001 0010 0100 1000;

#每个进程最大打开文件数
worker_rlimit_nofile 51200;

#进程号保存文件
pid    /var/run/nginx.pid;

events {
    #使用epoll（linux2.6的高性能方式）
    use epoll;
    #每个进程最大连接数（最大连接=连接数x进程数）
    worker_connections  51200;
}

http {

#文件扩展名与文件类型映射表
include       mime.types;

#默认文件类型
default_type  text/html;

#curl 上传文件的路径
client_body_temp_path /data0/upload 1 2;

#设置上传文件的最大限制
client_max_body_size 1024m;

#日志文件格式
#log_format  main  '$remote_addr - $remote_user [$time_local] $request '
#                  '"$status" $body_bytes_sent "$http_referer" '
#                  '"$http_user_agent" "$http_x_forwarded_for"';

## Log Format
log_format main         '$remote_addr - $remote_user [$time_local] '
                        '"$request" $status $body_bytes_sent '
                        '"$http_referer" "$http_user_agent" '
                        '"$http_Cdn_Src_Ip" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time"';

        log_format cdn         '$http_x_forwarded_for - $remote_user [$time_local] '
                                '"$request" $status $bytes_sent '
                                '"$http_referer" "$http_user_agent" '
                                '"$remote_addr" "$gzip_ratio" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time"';

        log_format zwccdn       '$http_x_forwarded_for - $remote_user [$time_local] '
                    '"$request" $status $bytes_sent '
                    '"$http_referer" "$http_user_agent" "$host" '
                    '"$remote_addr" "$gzip_ratio" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time"  "$upstream_cache_status"';

        log_format  zwc   '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time"';

        log_format zwclogtest         '$http_x_forwarded_for - $remote_user [$time_local] '
                                '"$request" $status $bytes_sent '
                                '"$http_referer" "$http_user_agent" '
                                '"$remote_addr" "$gzip_ratio" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time" "$host"'
                                ' "VUID=$cookie_VUID; NAGENTID=$cookie_NAGENTID; JSESSIONID=$cookie_JSESSIONID; CPLOGIN=$cookie_CPLOGIN; AUM=$cookie_AUM;'
                                ' SEO_SEARCH_WEBSITE=$cookie_SEO_SEARCH_WEBSITE; SEO_SEARCH_KEYWORD=$cookie_SEO_SEARCH_KEYWORD;'
                                ' SEO_SEARCH_TARGET_URL=$cookie_SEO_SEARCH_TARGET_URL"';

    log_format zwcfluentd       '"$host" $http_x_forwarded_for - $remote_user [$time_local] '
                    '"$request" $status $bytes_sent '
                    '"$http_referer" "$http_user_agent" '
                    '"$remote_addr" "$gzip_ratio" "$upstream_addr" "$upstream_status" "$request_time" "$upstream_response_time"';

#日志文件
access_log  /dev/null;
error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#长链接超时时间
keepalive_timeout  30;

#打开gzip压缩
gzip  on;
#最小压缩文件大小
gzip_min_length  1000;
#压缩缓冲区
gzip_buffers     4 8k;
#压缩类型
gzip_types       text/* text/css application/javascript application/x-javascript;
#压缩比率
gzip_comp_level  9;
#压缩通过代理的所有文件
gzip_proxied     any;
#vary header支持
gzip_vary        on;
#压缩版本（默认1.1，前端为squid2.5使用1.0）
gzip_http_version 1.0;
#输出缓冲区
output_buffers   4 32k;
#输出拆包大小
postpone_output  1460;

#接收header的缓冲区大小
client_header_buffer_size 128k;
large_client_header_buffers 4 256k;

server_names_hash_bucket_size 512;

#客户端发送header超时
client_header_timeout  10m;
#客户端发送内容超时
client_body_timeout    10m;
#发送到客户端超时
send_timeout           10m;
#开启高效文件传输模式
sendfile                on;
#捕捉代理端的http错误
#proxy_intercept_errors  on;
#默认编码
charset utf-8;

#support shtml
ssi on;
ssi_silent_errors on;
ssi_types text/shtml;

#proxy_cache_path   /tmp/ng_proxy_cache_dir levels=1:2  keys_zone=proxy_cache:200m inactive=7d max_size=5G;
#proxy_temp_path   /tmp/ng_proxy_temp_dir;

#fastcgi_temp_path   /tmp/ng_fastcgi_temp_dir;
#fastcgi_cache_path /tmp/ng_fastcgi_cache_dir levels=1:2 keys_zone=fastcgi_cache:200m inactive=7d max_size=5G;

##应用防火墙
#lua_code_cache off;
#init_by_lua_file  /usr/local/nginx/conf/init.lua;
#access_by_lua_file /usr/local/nginx/conf/waf.lua;
#lua_shared_dict updatedict 10m;

#upstream配置文件
include /usr/local/nginx/conf/upstream.conf;

include /usr/local/nginx/conf/vhost/*.conf;

}
```
###设置代理配置文件
```
#vim /usr/local/nginx/conf/proxy_store_off.conf
proxy_redirect off;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
client_max_body_size 50m;
client_body_buffer_size 256k;
proxy_connect_timeout 300;
proxy_send_timeout 300;
proxy_read_timeout 300;
proxy_buffer_size 4k;
proxy_buffers 4 32k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 64k;
proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
proxy_max_temp_file_size 128m;

proxy_hide_header Expires;
proxy_hide_header Pragma;
proxy_hide_header Cache-Control;

proxy_store off;
```
###Web host案例
```
cd /usr/local/nginx/conf/vhost/
root># cat quant.hstong.com.conf
server
{
      listen          80;
      server_name     daily.quant.hstong.com;

      index index.jsp index.html index.htm ;
      access_log  /usr/local/nginx/logs/daily.quant.hstong.com.log  zwccdn;
      error_log   /usr/local/nginx/logs/daily.quant.hstong.com.error.log info;

      location ~* ^/webapi {
             include /usr/local/nginx/conf/proxy_store_off.conf;
             add_header  Cache-Control  no-cache;
             expires -1;
             proxy_pass http://quant-webapi;
          }

      location / {
             include /usr/local/nginx/conf/proxy_store_off.conf;
             add_header  Cache-Control  no-cache;
             expires -1;
             proxy_pass http://quant-webclient;
          }
}
```

###添加域名vhost配置文件
```
cd /usr/local/nginx/conf/vhost/
root># cat dbgw.inzwc.com.conf
upstream zwcdbgw {
    server unix:///tmp/uwsgi_dbgw.sock;
}

server {
    listen          80;
    server_name     localhost dbgw.inzwc.com;
    rewrite ^(.*)$  https://$host$1;
}

# server {
#     listen          80;
#     server_name     localhost dbgw.inzwc.com;
#     set $purge_uri $request_uri;
#     index index.jsp index.html index.htm ;
#     root /data0/www/dbgw.inzwc.com;
#
#     error_page 405 =200 @405;
#     access_log  /usr/local/nginx/logs/dbgw.inzwc.com.log  zwccdn;
#     error_log   /usr/local/nginx/logs/dbgw.inzwc.com.error.log info;
#
#     location / {
#         include /usr/local/nginx/conf/uwsgi_params;
#         uwsgi_pass zwcdbgw;
#         uwsgi_param UWSGI_PYHOME /home/www/dbgw/venv/;
#         uwsgi_param UWSGI_CHDIR  /home/www/dbgw/;
#         uwsgi_param UWSGI_SCRIPT run:app;
#     }
#
#     dav_methods PUT;
# }

server {
    listen          443;
    server_name     localhost dbgw.inzwc.com;
    set $purge_uri $request_uri;
    index index.jsp index.html index.htm ;
    root /data0/www/dbgw.inzwc.com;

    ssl on;
    ssl_certificate /home/www/dbgw/cert/ssl/dbgw.inzwc.com.crt;
    ssl_certificate_key /home/www/dbgw/cert/ssl/dbgw.inzwc.com.key;
    ssl_session_timeout 10m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers  ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-RC4-SHA:ECDHE-RSA-RC4-SHA:ECDH-ECDSA-RC4-SHA:ECDH-RSA-RC4-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES256-SHA:RC4-SHA;
    ssl_prefer_server_ciphers on;

    error_page 405 =200 @405;
    access_log  /usr/local/nginx/logs/dbgw.inzwc.com.log  zwccdn;
    error_log   /usr/local/nginx/logs/dbgw.inzwc.com.error.log info;

    location / {
        include /usr/local/nginx/conf/uwsgi_params;
        uwsgi_pass zwcdbgw;
        uwsgi_param UWSGI_PYHOME /home/www/dbgw/venv/;
        uwsgi_param UWSGI_CHDIR  /home/www/dbgw/;
        uwsgi_param UWSGI_SCRIPT run:app;
        uwsgi_connect_timeout 10m;
        uwsgi_read_timeout 10m;
        uwsgi_send_timeout 10m;
    }

    dav_methods PUT;
}

```
###测试nginx是否能正常启动
```
root># service nginx test
the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
configuration file /usr/local/nginx/conf/nginx.conf test is successful

root># /etc/init.d/nginx test
the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
configuration file /usr/local/nginx/conf/nginx.conf test is successful
```


## 报错问题：

    1、Git版本导致的

    root># git clone https://github.com/simplresty/ngx_devel_kit.git
    Initialized empty Git repository in /usr/local/src/ngx_devel_kit/.git/
    error:  while accessing https://github.com/simplresty/ngx_devel_kit.git/info/refs

    fatal: HTTP request failed
    
    #问题原因是：是curl 版本问题，更新curl版本后问题解决（或者升级git版本）
    
    yum update -y nss curl libcurl

```



在nginx.conf的http段添加    
    #waf
    lua_package_path "/usr/local/nginx/conf/waf/?.lua";
    lua_shared_dict limit 10m;
    init_by_lua_file  /usr/local/nginx/conf/waf/init.lua;
    access_by_lua_file /usr/local/nginx/conf/waf/waf.lua;
```


waf地址：

https://github.com/loveshell/ngx_lua_waf

http://blog.csdn.net/qq_25551295/article/details/51744815

