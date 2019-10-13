---
title: AnyConnect 好評如潮!
id: 1965
categories:
  - 網際網路
date: 2015-01-06 00:50:00
tags:
---

最近幾天被各種 AnyConnect 的消息反覆轟炸, 終於下定決心試試傳說中萌萌噠的 AnyConnect.

伺服器的部署參考鳳凰菊苣的網誌[《在 Ubuntu 服务器上搭建 OpenConnect 服务器小记》](https://blog.phoenixlzx.com/2014/07/21/setup-openconnect-server-on-ubuntu/).

不過值得注意的是, 如果使用更新版本的 OpenConnect Server, config 中應當包含 `socket-file` 和 `device` 兩項, 否則啓動 ocserv 會失敗.

另外, 路由表相關的設定可以刪除, 這樣就可以實現科學上網了（AnyConnect 的 iOS Client 只支持最多64條路由設定, 因此無法用 chnroute 產生的 IP 列表來設定自動切換）.

最後說一下 Mac OS X 上的使用.

Homebrew 中收錄了 OpenConnect Client, 在沒有安裝 TUNTAP Device 的 OS X 10.10 上運行良好, 唯一不方便的就是沒有 GUI 工具, CLI 使用方法如下：

```
$ sudo openconnect --user=username --cafile=/path/to/your/ca-cert.pem ladder.abc.com
```
