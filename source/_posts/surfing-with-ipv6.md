---
title: 暢遊·IPv6
id: 1760
categories:
  - 互聯網絡
date: 2014-02-01 16:37:35
tags:
---

現在 IPv6風頭很盛，於是在下決定也體驗一把。

TunnelBroker，he.net 旗下的免費 IPv6 Tunnel Broker。

[caption id="attachment_1761" align="alignnone" width="300"][![Hurricane Electric](/wp-content/uploads/2014/02/helogo-300x73.gif)](/wp-content/uploads/2014/02/helogo.gif) Hurricane Electric[/caption]

什麼，你不知道 he.net？這是一家 ISP，7in0.me 就在使用 he 家的 DNS。

<!--more-->

## 創建 IPv6 Tunnel Broker

首先，到 [TunnelBroker](https://tunnelbroker.net) 註冊帳號（有 he.net 帳號者可直接登入）。

記下你的 User ID

[caption id="attachment_1762" align="alignnone" width="300"][![TunnelBroker Main Page](/wp-content/uploads/2014/02/QQ20140201-1@2x-300x192.png)](/wp-content/uploads/2014/02/QQ20140201-1@2x-e1391243938494.png) TunnelBroker Main Page[/caption]

選左側 Create Regular Tunnel，填寫你的 IPv4 位址，選擇一個和你的距離比較靠近的節點，按一下“Create Tunnel”

[caption id="attachment_1763" align="alignnone" width="241"][![TunnelBroker Create New Tunnel](/wp-content/uploads/2014/02/QQ20140201-3@2x-241x300.png)](/wp-content/uploads/2014/02/QQ20140201-3@2x-e1391244021845.png) TunnelBroker Create New Tunnel[/caption]

重點是下面用紅色線條標識的信息

[caption id="attachment_1764" align="alignnone" width="300"][![TunnelBroker Tunnel Details](/wp-content/uploads/2014/02/QQ20140201-4@2x-300x275.png)](/wp-content/uploads/2014/02/QQ20140201-4@2x-e1391244044353.png) TunnelBroker Tunnel Details[/caption]

## 設定

你可以通過 Example Configurations 讓系統教你設定。

我使用搭載 DD-WRT 的路徑器，因此在下面以 DD-WRT 設定方法為例講解一下

進入 DD-WRT Web 界面，“系統管理”——“基本管理”——“IPv6 支援”

啟用 IPv6 和 Radvd，並鍵入如下設定信息（粗體紅色部分使用之前的信息替換）：

```
interface br0 {
  AdvSendAdvert on;
  prefix <span style="color: #ff0000;">**Routed /64**</span> {
    AdvOnLink on;
    AdvAutonomous on;
  };
};
```

然後，進入“系統管理”——“指令”，編輯啟動指令（其中，MTU 請到 “Tunnel Details” 的 “Advanced” 查找）

TunnelBroker 調整了安全設定，目前網上流傳的指令無法完成自動更新 IPv4 位址信息的工作，下面的指令是7IN0修改後的新版指令。

```
sleep 10
insmod ipv6
USERID="<span style="color: #ff0000;">**User ID**</span>"
PASSWORD="<span style="color: #ff0000;">**MTU**</span>"
TUNNELID="<span style="color: #ff0000;">**Tunnel ID**</span>"
while [ true ]
do
IPV4=$(ip -4 addr show dev ppp0 | awk '/inet / {print $2}' | cut -d/ -f1)
if [ "$IPV4" != "$OLDIP" ]
then
/wp-content/bin/wget 'http://ipv4.tunnelbroker.net/ipv4_end.php?ipv4b='$IPV4'&amp;pass='$PASSWORD'&amp;user_id='$USERID'&amp;tunnel_id='$TUNNELID -O -
echo "`date` ip address changed" &gt;&gt; /tmp/startup.debug
ip tunnel del he-ipv6
ip tunnel add he-ipv6 mode sit remote <span style="color: #ff0000;">**Server IPv4 Address**</span> local $IPV4 ttl 255
ip link set he-ipv6 up
ip addr add <span style="color: #ff0000;">**Server IPv6 Address**</span> dev he-ipv6
ip -6 addr add <span style="color: #ff0000;">**Routed /64（在::之後添加“1”，如2001:470:19:456::1/64）**</span> dev br0
ip route add ::/0 dev he-ipv6
ip route add <span style="color: #ff0000;">**Routed /64**</span> dev br0
fi

if [ `ps | grep radvd | grep -vc grep` -ne 1 ]
then
radvd -C /tmp/radvd.conf &amp;
echo "`date` process radvd not found, restarting" &gt;&gt; /tmp/startup.debug
fi
OLDIP="$IPV4"
sleep 60
done
```

全部保存後重啟路徑器。

最後的最後：在你的電腦中設定 IPv6 DNS，比如 2001:470:20::2