---
title: Ubuntu 16.04 上 Ejabberd extauth 設定
id: 2922
categories:
  - 應用技巧
date: 2017-09-20 12:59:40
tags:
---

Ejabberd 支援通過執行外部程式自定義外部認證邏輯, ejabberd 的[網頁](https://www.ejabberd.im/extauth)上提供了一些第三方程式碼可供參考.詳細的說明在 [ejabberd Developers Guide](https://www.ejabberd.im/files/doc/dev.html#htoc9).

<!--more-->

以 ejabberd Developers Guide 中提供的 python 範例為基礎進行改進, 首先修改 python 版本為 python3.

```
#!/usr/bin/python
```

修改為

```
#!/usr/bin/env python3
```

對於 python3 不同於 python2 的部分做如下修改：

```
...
    (size,) = unpack('>h', input_length)
...
def to_ejabberd(bool):
    answer = 0
    if bool:
        answer = 1
    token = pack('>hh', 2, answer)
    sys.stdout.write(token)
    sys.stdout.flush()
```

修改為

```
...
    (size,) = unpack('>h', input_length.encode())
...
def to_ejabberd(result):
    if result:
        sys.stdout.write('\x00\x02\x00\x01')
    else:
        sys.stdout.write('\x00\x02\x00\x00')
    sys.stdout.flush()
```

之後可對`auth()`, `isuser()`, `setpass()`做改動以適應自己的認證邏輯.其中`isuser()`和`setpass()`可以保留`return True`.

最後將修改好的檔案丟去`/etc/ejabberd/extauth.py`, 修改 owner 為`ejabberd`, 添加執行權限.

在`/etc/ejabberd/ejabberd.yml`找到`auth_method: internal`修改為：

```
auth_method:
  - internal
  - external
```

在其下方增加：

```
extauth_program: "/etc/ejabberd/extauth.py"
extauth_instances: 3
auth_use_cache: false
```

通過`ejabberdcctl`重新啟動`ejabberd`使設定生效.

若出現`extauth script has exitted abruptly with reason 'normal'`, 在排除檔案 owner 和執行權限有誤的情況下, 可能為`apparmor`所致（帶有 SELinux 的作業系統中可能為 SELinux 所致, 可以通過`sudo setenforce 0`臨時關閉 SELinux 測試）, 查看`/var/log/syslog`可看到相關 log.

```
Sep 19 11:31:45 localhost kernel: [ 5031.995813] audit: type=1400 audit(1505827901.939:57): apparmor="DENIED" operation="exec" profile="/usr/sbin/ejabberdctl" name="/var/lib/ejabberd/extauth.py" pid=4419 comm="sh" requested_mask="x" denied_mask="x" fsuid=100 ouid=100
```

臨時解決方法是移除`apparmor`.

```
sudo apt-get purge --auto-remove apparmor
```
