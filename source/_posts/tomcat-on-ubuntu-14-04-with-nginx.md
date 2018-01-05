---
title: Ubuntu 14.04 下 Nginx 作反代部署 Tomcat 應用
id: 1968
categories:
  - 互聯網絡
  - 應用技巧
date: 2015-03-04 22:56:00
tags:
---

<!--markdown-->最近有一個 Tomcat 應用需要部署到現有 VPS 上，該 VPS 已經安裝了 Nginx 和 HHVM，綜合考慮多種因素，決定不安裝 Apache，通過 Nginx 反代 Tomcat。

<!--more-->

### 安裝 Nginx

```
$ sudo apt update
$ sudo apt install nginx
```

### 安裝 Tomcat 7

```
$ sudo apt install tomcat7
$ sudo vim /etc/tomcat7/server.xml
...
&lt;Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           URIEncoding="UTF-8"
           redirectPort="8443" /&gt;
...
$ sudo apt-get install tomcat7-docs tomcat7-admin tomcat7-examples
$ sudo vim /etc/tomcat7/tomcat-users.xml
&lt;tomcat-users&gt;
    &lt;user username="user" password="password" roles="manager-gui,admin-gui"/&gt;
&lt;/tomcat-users&gt;
$ sudo service tomcat7 start
```

### 設定 Nginx

```
$ sudo vim /etc/nginx/sites-enabled/default
...
    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
        # Uncomment to enable naxsi on this location
        #include /etc/nginx/naxsi.rules;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:8080;
    }
...
```

然後可以使用 `http://127.0.0.1/manager/html `和 `http://127.0.0.1/host-manager/html `管理 tomcat 。