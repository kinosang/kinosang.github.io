---
title: Boxbilling 多語言設置問題
id: 2125
categories:
  - 應用技巧
date: 2015-12-02 01:12:56
tags:
---

前些日子我發現了一款很棒的開源 billing 程式—— Boxbilling, 通過簡單的嘗試, 我對於這款程式很滿意, 尤其是它物件導向的開發思想, 個人感覺比 WHMCS 高到不知哪裡去了.

但是我發現它的多語言功能好像不能正常工作, 通過多方查詢, 我瞭解到除了準備 .po 和 .mo 檔之外, 還要設定伺服器的 locales 才行.

<!--more-->

以 Ubuntu 14.04 為例, 修改 /var/lib/locales/supported.d/local 之後更新 locales：

```
$ sudo vim /var/lib/locales/supported.d/local
en_US.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8

$ sudo dpkg-reconfigure locales
```

最後 restart php-fpm（使用 Apache mod-php 的則 restart apache）

問題完美解決!
