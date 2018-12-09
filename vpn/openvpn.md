 # 一、Centos7.4 搭建 openvpn

    openvpn有两种模式，桥接模式和路由模式：

    桥接模式相当于网关模式，只需要内网一台服务器做server段，那么客户端就可以通过server访问内网的所有服务器；

    路由模式则是一对一的关系，客户端只能访问安装了vpn server段的服务器，这里讲的是openvpn桥接模式的搭建

 参考文档：
 
 https://blog.rj-bai.com/post/132.html#menu_index_11
