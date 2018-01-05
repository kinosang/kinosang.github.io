---
title: 更換域名到7in0.me並更換伺服器
id: 1480
categories:
  - 雜物箱
date: 2014-01-07 22:09:21
tags:
---

今天和朋友聊天時心血來潮想要註冊一個.me域名，拿前幾天新想出來的暱稱7in0san9一試，可以註冊，但是朋友建議弄短一點，於是又試了7in0.me，沒想到也是可用，於是趕緊拿下。

由於 Kinosang 差不多一年沒有打理這個網誌，友鏈大多已經失效，Google PR 也降到了0，更換域名想來不會照成什麼大影響，於是就將 kinosang.ws 直接301到現在的域名上了。

之前的郵箱地址不會棄用，但是新增加了7in0@kinosang.ws和me@7in0.me兩個別名。考慮到日後將 kinosang.ws 作為團隊主頁，因此不再使用 me@kinsoang.ws 發信。

<!--more-->

另外為了穩定性考慮，7in0.me 現在遷移到了 [DigitalOcean](https://www.digitalocean.com/?refcode=3a07c678a1d7 "DigitalOcean") 的 New York II 節點上。為了能選出一個延遲比較低的 IP 位址，我創建了數個 Droplet 才得到比較理想的結果。

[![選擇Droplets](/wp-content/uploads/2014/01/螢幕快照-2014-01-07-19.33.23-300x206.png)](/wp-content/uploads/2014/01/螢幕快照-2014-01-07-19.33.23.png)

&nbsp;

這裏需要提醒大家的一點是：DO 會把你自己已經移除的 Droplet 的 IP 位址分配給你新建的相同節點下的 Droplet，因此，如果想要更換 IP 位置，需要保留現有 Droplet 的情況下新建 Droplet，然後再移除舊的 Droplet。