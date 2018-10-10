## 一、设置主机host
```
cat > /etc/hosts <<EOF
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.56.10 master master.example.com
192.168.56.20 node1  node1.example.com
192.168.56.30 node2  node2.example.com
EOF
```

