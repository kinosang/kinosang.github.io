---
title: 在 macOS 上使用 Dnsmasq 進行測試
id: 2924
categories:
  - 應用技巧
date: 2017-11-25 18:07:17
tags:
---

通常我們需要修改`/etc/hosts`以進行測試, 例如`127.0.0.1 abc.dev`, 有些時候使用 Gas Mask 可以更方便一些.

但是這樣還是需要頻繁修改hosts檔.

可以改用 Dnsmasq 來更方便地解決問題.

<!--more-->

主要思路：

1.  安裝 Dnsmasq
2.  設定 Dnsmasq 解析`*.dev`為`127.0.0.1`
3.  設定 macOS 使用 Dnsmasq 處理`.dev`域

### 安裝 Dnsmasq

使用 Homebrew 安裝

```
$ brew install dnsmasq
```

### 設定 Dnsmasq

```
$ vim /usr/local/etc/dnsmasq.conf

# address=/double-click.net/127.0.0.1
address=/dev/127.0.0.1

$ sudo brew services start dnsmasq

$ dig abc.dev @127.0.0.1
;; ANSWER SECTION:
abc.dev. 0 IN   A   127.0.0.1
```

### 設定 macOS

```
$ sudo mkdir -p /etc/resolver
$ sudo vim /etc/resolver/dev
nameserver 127.0.0.1
```

### 測試

```
$ ping he.net
$ ping abc.dev
$ ping foo.bar.dev
```
