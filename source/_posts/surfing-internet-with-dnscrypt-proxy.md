---
title: 科學上網 - DNS 加密代理
id: 1734
categories:
  - 資安技術
date: 2014-01-25 11:56:46
tags:
---

DNS 加密代理——這玩意兒應對 DNS 污染很有用。

它在本地建立一個 DNS 伺服器，然後當有解析請求時，將請求通過加密的 TCP 信道傳遞給 OpenDNS 等服務商，然後將返回的結果解碼後供計算機使用。

[caption id="attachment_1751" align="alignnone" width="190"][![DNS 加密代理](/wp-content/uploads/2014/01/DNSCrypt.jpg)](/wp-content/uploads/2014/01/DNSCrypt.jpg) DNS 加密代理[/caption]

<!--more-->

<span style="line-height: 1.5;">讓我們來看一下如何安裝</span>

## Mac OS X

Mac 下使用 Homebrew 進行安裝

```
$ brew install dnscrypt-proxy
$ sudo cp -fv /wp-content/local/opt/dnscrypt-proxy/*.plist /Library/LaunchDaemons
$ sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.dnscrypt-proxy.plist
```

然後設定系統的 DNS 伺服器為 127.0.0.1

_如果在學校或公司等需要使用網絡時需要設定內網 DNS，將內網 DNS 排列在之下_

測試一下：

[caption id="attachment_1749" align="alignnone" width="300"][![測試 DNSCrypt-proxy 是否工作正常](/wp-content/uploads/2014/01/螢幕快照-2014-01-25-11.27.19-300x210.png)](/wp-content/uploads/2014/01/螢幕快照-2014-01-25-11.27.19-e1390620613906.png) 測試 DNSCrypt-proxy 是否工作正常[/caption]

上圖中“debug.opendns.com text = "dnscrypt enabled (...)"”即代表 DNSCrypt-proxy 工作正常。

## Linux

1.  下载 DNSCrypt-proxy 原始码（.tar.bz2檔）（点击[这里](http://download.dnscrypt.org/dnscrypt-proxy/)）</p>
2.  安裝</p>

```
$ bunzip2 -cd dnscrypt-proxy-*.tar.bz2 | tar xvf -
$ cd dnscrypt-proxy-*
$ ./configure && make -j2
$ make install
```

<p>3.啟動

```
$ dnscrypt-proxy --daemonize
```

1.  修改 DNS 设定（同 Mac OS X）

## Windows

1.  下载 DNSCrypt-proxy for win（dnscrypt-proxy-win32-full-*.zip檔）（点击[这里](http://download.dnscrypt.org/dnscrypt-proxy/)）

2.  安裝

解開 zip 檔，cd 進入相應檔案夾，執行下面的指令：

```
cmd> dnscrypt-proxy.exe --install
```

1.  修改 DNS 设定（同 Mac OS X）
