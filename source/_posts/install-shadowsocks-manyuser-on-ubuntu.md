---
title: Shadowsocks Manyuser 安裝筆記
id: 2097
categories:
  - 應用技巧
date: 2015-06-16 18:03:58
tags:
---

### 安裝 Mariadb

過程略, 參見 往期網誌.

```
$ mysql -u root -p
MariaDB [(none)]> create database shadowsocks;
MariaDB [(none)]> CREATE USER 'shadowsocks'@'%' IDENTIFIED BY 'password';
MariaDB [(none)]> GRANT SELECT, INSERT, UPDATE, DELETE, INDEX ON shadowsocks.* TO 'shadowsocks'@'%';
MariaDB [(none)]> flush privileges;\q
```

<!--more-->

### 安裝依賴

```
$ sudo apt install python-pip python-m2crypto unzip vim
$ sudo pip install cymysql
```

### 安裝 Shadowsocks Manyuser

```
$ wget https://github.com/mengskysama/shadowsocks/archive/manyuser.zip
$ unzip manyuser.zip
$ cd shadowsocks-manyuser/shadowsocks/
$ vim Config.py
#Config
MYSQL_HOST = 'localhost'
MYSQL_PORT = 3306
MYSQL_USER = 'shadowsocks'
MYSQL_PASS = 'password'
MYSQL_DB = 'shadowsocks'

MANAGE_PASS = 'ss233333333'
#if you want manage in other server you should set this value to global ip
MANAGE_BIND_IP = '127.0.0.1'
#make sure this port is idle
MANAGE_PORT = 23333
```

### 優化 Shadowsocks

```
$ sudo vim /etc/sysctl.conf
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla

# for low-latency network, use cubic instead
# net.ipv4.tcp_congestion_control = cubic

$ sudo sysctl -p
$ ulimit -n 51200
```

### 增强安全性

```
$ sudo apt install wondershaper
# limit bandwidth to 10Mb/10Mb on eth0
$ wondershaper eth0 10000 10000
$ sudo apt install fail2ban
$ sudo vim /etc/fail2ban/filter.d/shadowsocks.conf
# Fail2Ban filter for shadowsocks

[Definition]

failregex = ERROR\s+can not parse header when handling connection from :\d+

ignoreregex =

$ sudo vim /etc/fail2ban/jail.d/shadowsocks.conf
[ssserver]

enabled = true
port = 10000:20000
filter = shadowsocks
logpath = /var/log/shadowsocks.log
maxretry = 6

$ sudo useradd ssuser
$ sudo iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 80 -j ACCEPT
$ sudo iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 443 -j ACCEPT
$ sudo iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -s 127.0.0.1 -d 127.0.0.1 -j ACCEPT
$ sudo iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp -j REJECT --reject-with tcp-reset
$ sudo apt install nginx
$ sudo vim /etc/nginx/sites-enabled/default
server {
listen 127.0.0.1:3128;
resolver 8.8.8.8;
location / {
set $upstream_host $host;
if ($request_uri ~ "^/announce.*") {
return 403;
}
if ($request_uri ~ "^.*torrent.*") {
return 403;
}
proxy_set_header Host $upstream_host;
proxy_pass http://$upstream_host;
proxy_buffering off;
}
}

$ sudo service nginx restart
$ sudo iptables -t nat -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-port 3128
```

### 啟動 Shadowsocks

```
$ sudo -u ssuser python server.py > /var/log/shadowsocks.log 2>&1 &
```
