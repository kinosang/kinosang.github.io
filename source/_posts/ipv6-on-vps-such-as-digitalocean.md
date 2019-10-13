---
title: 讓你的 IPv4 Only VPS（如DigitalOcean） 支援 IPv6
id: 1784
categories:
  - 網際網路
date: 2014-02-03 17:55:37
tags:
---

IPv6真的很好很強大，在下折騰了一天讓在下的小窩也支援 IPv6 了。你可以在 IPv6 環境中通過 7in0.me 或 ipv6.7in0.me（IPv6 Only） 存取本站。

[caption id="attachment_1785" align="alignnone" width="250"][![This website is IPv6 Ready](/wp-content/uploads/2014/02/ipv6_ready_logo.png-250x300.jpeg)](/wp-content/uploads/2014/02/ipv6_ready_logo.png.jpeg) This website is IPv6 Ready[/caption]

很多 VPS 目前仍然不支援 IPv6，但是，藉助 TunnelBroker，我們可以讓 VPS 擁有一組 IPv6 存取位址。

<!--more-->

## 一、取得 IPv6 位址

首先，你得為你的 VPS 創建一個 Regular Tunnel，參見在下的上一篇網址《[暢遊· IPv6](http://7in0.me/2014/02/surfing-with-ipv6/ "暢遊·IPv6")》。

**#下面的教程以 CentOS 為例，Debian/Ubuntu 等亦可照葫蘆畫瓢。**

然後，進入“Tunnel Details”——“Example Configurations”，在“Select Your OS”處選擇“Linux-route2”，將這些指令拿到 VPS 上面執行，並把它們寫到 /etc/rc.d/rc.local 的結尾。

## 二、設定 Nginx

如果你是用 Nginx，並且使用 LNMP 套件或其他原始碼編譯安裝的方法，那麽，你可能需要重新編譯安裝。

檢查方法：使用 nginx -v 指令，若回顯的結果中有

```
configure arguments: --with-ipv6
```

則可以跳過重新編譯安裝的步驟，否則需要重新編譯：在 configure 時添加 --with-ipv6 的選項。

接著，修改 Nginx 站點設定檔，在 server_name 上面添加一行：

```
listen  [::]:80 ipv6only=on;
```

最後執行：

```
$ service nginx reload
```

使修改生效

## 三、 防火牆設定

IPv6 不受 iptables 限制，但是受到 ip6tables 的限制，用下面的指令查看相關設定

```
$ ip6tables -L
```

然後可以根據個人需要，修改規則，指令同 iptables。

最後，別忘記儲存 =.=

```
$ service ip6tables save
```

## 四、設定 DNS AAAA 記錄

這一步很重要，如果不進行這一步的話，別人也就無法通過IPv6來存取你的網站。

以在下的小窩為例：

[caption id="attachment_1794" align="alignnone" width="300"][![在下小窩的 DNS 設定](/wp-content/uploads/2014/02/DNS-300x161.png)](/wp-content/uploads/2014/02/DNS-e1391423389454.png) 在下小窩的 DNS 設定[/caption]

在下創建了 7in0.me 的 A 記錄和 AAAA 記錄，其餘 SubDomain 均是 CNAME；

同時，在下添加了 ipv6.7in0.me 的 AAAA 記錄以創建一個 IPv6 Only 測試站點。
