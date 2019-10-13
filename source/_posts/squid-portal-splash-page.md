---
title: Squid Portal Splash 頁
id: 2610
categories:
  - 應用技巧
date: 2016-11-30 20:57:30
tags:
---

Squid 作爲一款成熟的代理伺服器軟體, 功能十分強大.但是因爲 Squid 屬於比較古老的項目, 官方 Wiki 或有沒有跟進改動的地方, 比如 Portal Splash.

Portal Splash 主要依賴 HTTP Status 511 和 ext_session_acl.Wiki 見 http://wiki.squid-cache.org/ConfigExamples/Portal/Splash.

<!--more-->

但是官方 Wiki 的範例存在問題, 導致修改設定後不能正常啓動 Squid.以下改動參考 http://squid-web-proxy-cache.1019090.n4.nabble.com/Portal-Splash-Pages-example-on-squid-3-3-13-td4669634.html.

以 “Active Mode”爲例, 修改 squid.conf 如下：

```
# Set up the session helper in active mode.
external_acl_type session ipv4 concurrency=100 ttl=3 %SRC /usr/lib64/squid/ext_session_acl -a -T 60 -b /var/lib/squid/session/

# Pass the LOGIN command to the session helper with this ACL
acl session_login external session LOGIN

# Normal session ACL as per simple example
acl session_is_active external session

# ACL to match URL
acl clicked_login_url url_regex -i ^http://example.com/$

# First check for the login URL. If present, login session
http_access allow clicked_login_url session_login

# If we get here, URL not present, so renew session or deny request.
http_access deny !session_is_active

# Deny page to display
deny_info 511:splash.html session_is_active

# And finally deny all other access to this proxy
http_access deny all

# Squid normally listens to port 3128
http_port 8080

# Uncomment and adjust the following to add a disk cache directory.
cache_dir ufs /var/spool/squid 100 16 256

# Leave coredumps in the first cache dir
coredump_dir /var/spool/squid
```

記得檢查 `/var/lib/squid/session/` 是否存在, 如不存在, 需要手動建立並修改owner, 否則會出現 `The session helpers are crashing too rapidly, need help!`.

```
$ sudo mkdir -p /var/lib/squid/session/
$ sudo chown -R squid:squid /var/lib/squid/session/
```

在重新啓動 Squid 之前, 應當創建 `/usr/share/squid/errors/templates/splash.html`, 內容如下：

```

<html>
 <head>
 <title>splash screen example</title>
 </head>
 <body>
 This is splash screen
 </body>
</html>
```

p.s. 在 Squid 手冊 SessionHelper 小節中發現新的官方範例 http://wiki.squid-cache.org/EliezerCroitoru/SessionHelper/Conf
