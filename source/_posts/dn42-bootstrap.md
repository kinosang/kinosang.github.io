---
title: DN42 入門指南
id: 2138
categories:
  - 網際網路
date: 2016-04-09 16:33:32
tags:
---

[![dn42](https://blog.7in0.me/wp-content/uploads/2016/04/dn42.png)](https://blog.7in0.me/wp-content/uploads/2016/04/dn42.png)

### 什么是 dn42

dn42 是一個使用多種因特網技術（包括 BGP, whois 資料庫, DNS 等）構建的大型的動態 VPN 網路.參與者使用隧道技術（比如 GRE, OpenVPN, Tinc, IPsec）與其他人互相連線並利用BGP（邊界閘道器協議）交換路由.

dn42 使用 172.20.0.0/14 , fd00::/8 和私有ASN.

dn42 也與其他網路, 比如 ChaosVPN 和部分 Freifunk 網路.

<!--more-->

### 基礎需求

*   至少一個 7x24 小時運行的路由器.Linux 或 BSD 盒子可以用作路由器.如果你的家用路由器使用 OpenWRT, 你可以考慮在 dn42 中使用該裝置.
*   你的路由器可以通過因特網接入隧道服務（GRE, OpenVPN, IPsec, Tinc 等）.要注意, 在學校或其他地方, 你的網路管理員會過濾這些流量.
*   你具備一定的網路和路由器知識, 比如BGP, IGP, 轉發.並且你具備配置 BGP 路由器（比如 Quagga 和 Bird）的能力.

### 接入手續

一, 加入 mail list

*   郵箱 dn42@lists.nox.tf
*   NNTP alt.net.dn42.users
*   存檔 http://lists.nox.tf/pipermail/dn42/
*   發送任意電郵給 dn42-subscribe@lists.nox.tf 加入串列

你也可以同時加入 IRC 頻道 #dn42 @ hackint.org

二, 註冊

你需要通過表單註冊一系列項目.推薦使用[網頁](https://io.nixnodes.net/?registry)來進行, 你也可以使用 [monotone](https://dn42.net/services/Whois#monotone).

假定的名字是 FOO , 是 FOO-ORG 機構的一員.下面的範例將會使用這些名稱.

#### 首先要創建 維護者（maintainer）

你的個人信息, 機構信息和其他的項目（ASN, IP 子網, DNS 區域 等）都通過 mnt 項目管理.

按“Create Object”創建 mntner 項目, 輸入必備信息並按“Save”送出, 注意觀察右側下方信息是否報錯：

```
mntner:    FOO-MNT
descr:     簡介
sha512-pw: 密碼（明文, 非 sha512 指紋）
admin-c:   DUMMY-DN42 （稍後我們會更新該項, 下同）
tech-c:    DUMMY-DN42
mnt-by:    FOO-MNT （和 mntner 項相同）
```

#### 為你個人創建個人（person）

```
person:  foo foo 個人姓名或暱稱
contact: 電子郵箱地址, xmpp, irc 或 twitter 如 xmpp:foo@foo.com
nic-hdl: FOO-DN42 以 -DN42 結尾
mnt-by:  FOO-MNT
```

#### 更新 mntner

在 “Search the whois database” 鍵入 FOO-MNT 按 Enter 查詢之前創建的 mntner.

修改 admin-c 和 tech-c 並按“Save”送出.

#### 為你的機構創建機構（organisation）（若需要）

```
organisation: ORG-FOO
org-name:     機構名稱
descr:        機構簡介
e-mail:       dn42@foo.com 按“+”添加該項, 並填寫機構電郵或 mail list
www:          http://foo.com 按“+”添加該項, 並填寫機構網站
admin-c:      FOO-DN42
tech-c:       FOO-DN42
mnt-by:       FOO-MNT
```

#### 現在, 你可以使用下面的信息創建其他項目

個人項目：

```
admin-c: FOO-DN42
tech-c:  FOO-DN42
mnt-by:  FOO-MNT
```

機構項目：

```
admin-c: ORG-FOO
tech-c:  ORG-FOO
mnt-by:  FOO-MNT
```

#### 註冊 AS 號碼（ASN）

你的 ASN 必須在 dn42 的 AS 號碼空間中, 在 2014 年 6 月之前, 可選值為 64600-64855, 76100-76200**現在你必須**在 4242420000-4242423999 中選擇一個號碼來使用.

你可以在 http://ix.ucis.nl/dn42/as.php 查看已經被佔用的 ASN.

若你想使用一個不在上述號碼空間的 ASN, 請確認該號碼不會與 Freifunk, ChaosVPN 等網絡的 ASN 衝突.你可以在 http://nixnodes.net/dn42/graph/ 查看在 dn42 中廣播的 ASN.

若不確定要使用哪個 ASN, 你可以在 mailing list 或 IRC 詢問.

創建 aut-num 項目（省略了admin-c, tech-c 和 mnt-by）

```
aut-num: AS4242420000 按前述內容自行選擇 AS 號碼
as-name: FOO-AS
descr:   Foo's AS
```

#### 註冊子網

在 http://dn42.netrik.de/ 查看可以註冊的 IPv4 子網, 系統默認分配 /27 子網, 請儘可能不要使用比 /26 更大的子網, 不要使用比 /25 更大的子網.dn42 的 IPv4 空間有限, 在註冊 IPv4 子網之前, 仔細思考再進行選擇：

*   若 僅家用 或 只運行 2-3 台伺服器和兩個 VPN 空間, /28 子網就夠用了
*   若需要 /24 或更大的子網, 請先在 mailing list 或 IRC 詢問

inetnum/172.23.75.0_24 範例：

```
inetnum: 172.23.75.0 - 172.23.75.255
netname: FOO-NETWORK
descr:   Network of FOO
country: CN
nserver: ns1.foo.dn42
nserver: ns2.foo.dn42
status:  ASSIGNED
```

若需要 IPv6, 你可以通過 http://unique-local-ipv6.com/ 生成一個 IPv6 子網.

inet6num/fd42:4992:6a6d::_48 範例：

```
inet6num: fd42:4992:6a6d:0000:0000:0000:0000:0000 - fd42:4992:6a6d:ffff:ffff:ffff:ffff:ffff
netname:  FOO-NETWORK
descr:    Network of FOO
country:  CN
nserver:  ns1.foo.dn42
nserver:  ns2.foo.dn42
status:   ASSIGNED
```

#### 創建路由器

若要廣播你的子網, 需要為他們創建路由器.

route/172.23.75.0_24:

```
route:      172.23.75.0/24
origin:     AS4242420000
mnt-by:     FOO-MNT
bgp-status: active
```

route6/fd42:4992:6a6d::_48

```
route6: fd42:4992:6a6d::/48
origin: AS4242420000
mnt-by: FOO-MNT
```

### 下一步（待整理）

取得 peers

接入隧道

運行路由伺服

設定 DNS

使用或創建網際服務

你們可以在“[關於我](https://blog.7in0.me/7in0/)”找到我的 dn42 ASN 等信息.
