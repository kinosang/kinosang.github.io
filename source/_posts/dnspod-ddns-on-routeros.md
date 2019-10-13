---
title: RouterOS DNSPod DDNS 設定
categories:
  - 網際網路
date: 2019-10-13 16:36:37
tags:
---

最近開始使用 MikroTik RouterOS, 檢索發現 DNSPod DDNS 設定多依賴某轉發服務[^1].

這種行為是十分不安全的, 故自行研究方法.

<!--more-->

MikroTik Wiki 中有如何設定 NO-IP DDNS 的說明[^2], 從其程式碼修改即可適配 DNSPod.

```
##############Script Settings##################

:local DNSPODTOKENID "DNSPod API Token ID"
:local DNSPODTOKEN "DNSPod API Token"
:local DNSPODDOMAIN "DNSPod Domain Name"
:local DNSPODRECORD "DNSPod Record ID"
:local DNSPODSUBDOMAIN "DNSPod SubDomain Name"
:local WANInterface "MikroTik Router WAN Interface Name"

###############################################

:local DdnsDomain "$DNSPODSUBDOMAIN.$DNSPODDOMAIN"
:local IpCurrent [/ip address get [find interface=$WANInterface] address];
:for i from=( [:len $IpCurrent] - 1) to=0 do={
  :if ( [:pick $IpCurrent $i] = "/") do={
    :local NewIP [:pick $IpCurrent 0 $i];
    :if ([:resolve $DdnsDomain] != $NewIP) do={
      /tool fetch mode=https url="https://dnsapi.cn/Record.Ddns" http-data="login_token=$DNSPODTOKENID%2C$DNSPODTOKEN&domain=$DNSPODDOMAIN&record_id=$DNSPODRECORD&sub_domain=$DNSPODSUBDOMAIN&record_line_id=0&value=$NewIP" keep-result=no
      :log info "NO-IP Update: $DdnsDomain - $NewIP"
     }
   }
}
```

雖然可以通過 RouterOS Schedule 定時執行上述程式碼, 但是這種方法一點也不優雅.

經過搜尋發現, 可以利用 PPP Profile 的 ON-UP Script[^3].

於是將上述程式碼放到 `pppoe-out1` 使用的 profile 中, 並在開頭新增 `:delay 10`.

這樣每次 PPPoE 撥號成功就會自動更新 DDNS 了.

*同理，WIKI 上 NO-IP 的程式碼也可以放在這裡使用.*

[^1]: 不點名批評
[^2]: https://wiki.mikrotik.com/wiki/Dynamic_DNS_Update_Script_for_No-IP_DNS
[^3]: https://forum.mikrotik.com/viewtopic.php?t=124990
