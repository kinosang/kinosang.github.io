---
title: haproxy、nginx-naxsi 和 fail2ban 緩解網路攻擊
id: 1967
categories:
  - 互聯網絡
  - 安全技術
  - 應用技巧
date: 2015-02-26 13:32:00
tags:
---

這篇博文是根據回憶所寫，可能存在謬誤，歡迎大家指正。

### 系統簡介

這套系統是一套高可靠性 WAF 平臺，讓 haproxy, Naxsi(Nginx 模塊) 和 fail2ban 協同工作來保護網路服務，對注入、XSS、CC、DDoS等攻擊方式有一定效果。
<!--more-->

### 系統結構

<pre>  訪客
   |
  \|/
1.2.3.4
 ======        ======
|ft_waf| ---&gt; |      |
|------|      |bk_waf|
|ft_web| &lt;--- |      |
 ======        ======
10.0.0.1      10.0.0.2
   |
  \|/
 ======
|bk_web|
 ======
10.0.0.3
</pre>

`ft_` 前綴系 HAProxy frontends
`bk_` 前綴系 HAProxy backends

### 具體設定

#### Nginx 和 Naxsi

Nginx 和 Naxsi 安裝在 bk_waf 上。
在 `nginx.conf` 啓用 Naxsi，修改 Nginx 默認站點文件如下：

<pre>server {
    proxy_set_header Proxy-Connection "";
    listen     10.0.0.2:80;

    set_real_ip_from 10.0.0.1;
    real_ip_header X-Client-IP;

    access_log /var/log/nginx/naxsi_access.log;
    error_log  /var/log/nginx/naxsi_error.log debug;

    location / {
        include    /etc/nginx/test.rules;
        proxy_pass http://10.0.0.1:81/;
    }

    error_page 403 /403.html;
    location = /403.html {
        root /opt/nginx/html;
        internal;
    }

    location /RequestDenied {
        return 403;
    }
}
</pre>

#### HAProxy

參考設定：
* DDoS 防禦
* 由 `bk_waf` 分析攻擊行爲並由 `ft_waf` 阻擋
* 當 `bk_waf` 不可用時自動跳過 WAF

<pre>defaults
    option  http-server-close
    option  dontlognull
    option  redispatch
    option  contstats
    retries 3
    timeout connect 5s
    timeout http-keep-alive 1s
    # Slowloris protection
    timeout http-request 15s
    timeout queue 30s
    timeout tarpit 1m          # tarpit hold tim
    backlog 10000

bind 1.2.3.4:80 name http
    mode http
    log global
    option httplog
    timeout client 25s
    maxconn 10000

    # DDOS protection
    # Use General Purpose Couter (gpc) 0 in SC1 as a global abuse counter
    # Monitors the number of request sent by an IP over a period of 10 seconds
    stick-table type ip size 1m expire 1m store gpc0,http_req_rate(10s),http_err_rate(10s)
    tcp-request connection track-sc1 src
    tcp-request connection reject if { sc1_get_gpc0 gt 0 }
    # Abuser means more than 100reqs/10s
    acl abuse sc1_http_req_rate(ft_web) ge 100
    acl flag_abuser sc1_inc_gpc0(ft_web)
    tcp-request content reject if abuse flag_abuser

    acl static path_beg /static/ /dokuwiki/images/
    acl no_waf nbsrv(bk_waf) eq 0
    acl waf_max_capacity queue(bk_waf) ge 1
    # bypass WAF farm if no WAF available
    use_backend bk_web if no_waf
    # bypass WAF farm if it reaches its capacity
    use_backend bk_web if static waf_max_capacity
    default_backend bk_waf

# WAF farm where users' traffic is routed first
backend bk_waf
    balance roundrobin
    mode http
    log global
    option httplog
    option forwardfor header X-Client-IP
    option httpchk HEAD /waf_health_check HTTP/1.0

    # If the source IP generated 10 or more http request over the defined period,
    # flag the IP as abuser on the frontend
    acl abuse sc1_http_err_rate(ft_waf) ge 10
    acl flag_abuser sc1_inc_gpc0(ft_waf)
    tcp-request content reject if abuse flag_abuser

    # Specific WAF checking: a DENY means everything is OK
    http-check expect status 403
    timeout server 25s
    default-server inter 3s rise 2 fall 3
    server waf1 10.0.0.2:80 maxconn 100 weight 10 check

# Traffic secured by the WAF arrives here
frontend ft_web
    bind 10.0.0.1:81 name http
    mode http
    log global
    option httplog
    timeout client 25s
    maxconn 1000
    # route health check requests to a specific backend to avoid graph pollution in ALOHA GUI
    use_backend bk_waf_health_check if { path /waf_health_check }
    default_backend bk_web

# application server farm
backend bk_web
    balance roundrobin
    mode http
    log global
    option httplog
    option forwardfor
    cookie SERVERID insert indirect nocache
    default-server inter 3s rise 2 fall 3
    option httpchk HEAD /
    # get connected on the application server using the user ip
    # provided in the X-Client-IP header setup by ft_waf frontend
    source 0.0.0.0 usesrc hdr_ip(X-Client-IP)
    timeout server 25s
    server server1 10.0.0.3:80 maxconn 100 weight 10 cookie server1 check

# backend dedicated to WAF checking (to avoid graph pollution)
backend bk_waf_health_check
    balance roundrobin
    mode http
    log global
    option httplog
    option forwardfor
    default-server inter 3s rise 2 fall 3
    timeout server 25s
    server server1 10.0.0.3:80 maxconn 100 weight 10 check
</pre>

#### Fail2ban

##### 於 `bk_waf` 上

創建 `/etc/fail2ban/filter.d/nginx-naxsi.conf` ：

<pre>[INCLUDES]
before = common.conf

[Definition]
failregex = NAXSI_FMT: ip=.*
ignoreregex =
</pre>

在 `/etc/fail2ban/jail.conf` 添加：

<pre>[nginx-naxsi]

enabled = true
port = http,https
filter = nginx-naxsi
logpath = /var/log/nginx/naxsi_error.log
maxretry = 6
</pre>

#### 參考資料

http://blog.haproxy.com/2012/10/16/high-performance-waf-platform-with-naxsi-and-haproxy/