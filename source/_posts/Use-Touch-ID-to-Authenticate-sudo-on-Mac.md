---
title: Mac 上使用 Touch ID 認證 sudo
date: 2018-05-02 18:18:43
categories:
  - 應用技巧
tags:
---

雖然網路上應該隨便搜索一下就可以找到相關內容，但是考慮到這裡長草許久，還是隨便寫點東西。

入手新 MacBook Pro with Touch Bar 之後，發現 Touch ID 十分好用，美中不足是 terminal 裡 `sudo` 不能享受這一便利。

<!--more-->

感謝 [sudo-touchid](https://github.com/mattrajca/sudo-touchid) 專案帶給我的便利，不過後來我發現了 macOS 自帶的隱藏功能。

`sudo` 指令是利用 pam 進行認證的，macOS 中提供了一個 `pam_tid.so` 檔，只要添加到 `sudo` 指令的 pam 設定中就可以了。

```bash
$ sudo vim /etc/pam.d/sudo

# sudo: auth account password session
auth       sufficient     pam_tid.so # 新增這行
auth       sufficient     pam_smartcard.so
```

因為 `/etc/pam.d/sudo` 檔唯讀，所以需要用 `:w!` 存儲。

現在就可以在使用 `sudo` 時按手指了。