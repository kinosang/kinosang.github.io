---
title: Cubieboard 入門筆記 I 安裝與TTY
id: 1606
categories:
  - 硬體研究
date: 2014-01-11 17:46:57
tags:
---

Arduino as usb2tty | tty 終端機 | Cubieboard | Cubian | NAND

今天稍早些時候, 我從內蒙古回到了濟寧.

之前從欣然兄手中收購了一塊 Cubieboard, 並拜託他刷好 Ubuntu.於是回到家的第一時間, 我便嘗試折騰這塊小巧可愛的板子.

先來張板子的特寫.

[caption id="attachment_1609" align="alignnone" width="300"][![Cubieboard 的靚照](/wp-content/uploads/2014/01/CIMG2470-300x225.jpg)](/wp-content/uploads/2014/01/CIMG2470-e1389433434671.jpg) Cubieboard 的靚照[/caption]

<!--more-->

不是 痛CPU 差評!

[caption id="attachment_1610" align="alignnone" width="300"][![可愛又實用的 痛CPU](/wp-content/uploads/2014/01/serious-300x195.jpg)](/wp-content/uploads/2014/01/serious.jpg) 可愛又實用的 痛CPU[/caption]

嘛嘛, 果然不能在意細節咩?

上電——開機——聯網……

一切正常!

然後……

```bash
$ ssh 192.168.1.5
ssh: connect to host 192.168.1.5 port 22: connection refused
```

誰能告訴我這是怎麽回事?!難不成是 Ubuntu 有問題了?

啊啊啊, 重灌成 Cubian 好了.（參考[Install Cubian](https://github.com/cubieplayer/cubian/wiki/Install-Cubian)）

[caption id="attachment_1611" align="alignnone" width="300"][![通過 dd 指令安裝 Cubie 到 SD 卡](/wp-content/uploads/2014/01/螢幕快照-2014-01-11-10.04.52-300x210.png)](/wp-content/uploads/2014/01/螢幕快照-2014-01-11-10.04.52-e1389433421151.png) 通過 dd 指令安裝 Cubie 到 SD 卡[/caption]

## TTL方式連接

先體驗一下使用 ttl 連接到我可愛的 cubieboard.

[caption id="attachment_1613" align="alignnone" width="300"][![把 Arduino 當做 usb2tty 組件（拉低Reset）](/wp-content/uploads/2014/01/CIMG2471-300x225.jpg)](/wp-content/uploads/2014/01/CIMG2471-e1389433363772.jpg) 把 Arduino 當做 usb2tty 組件（拉低Reset）[/caption]

[caption id="attachment_1612" align="alignnone" width="300"][![GND 接 GND, RX 接 RX, TX 接 TX, Vcc 懸空](/wp-content/uploads/2014/01/CIMG2469-e1389435395831-300x225.jpg)](/wp-content/uploads/2014/01/CIMG2469-e1389435395831.jpg) GND 接 GND, RX 接 RX, TX 接 TX, Vcc 懸空[/caption]

然後, 用 screen 連接

```bash
$ ls /dev/tty.*
/dev/tty.Bluetooth-Incoming-Port
/dev/tty.Bluetooth-Modem
/dev/tty.usbmodem1a1221
$ screen /dev/tty.usbmodem1a1221 115200 -U
Debian GNU/Linux 7 Cubian ttyS0

Cubie login: cubie
Password:
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /wp-content/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
cubie@Cubian:~$
```

成功連接, 開瓶香檳慶祝下!

## 灌到 NAND

```bash
$ sudo -i
$ apt-get install cubian-nandinstall
...
$ cubian-nandinstall
...
*** Success! remember to REMOVE your SD card from board ***
shutdown now? yes
...
```

拔掉 SD 卡然後開機, 搞定!

```bash
...
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
rootfs          3.8G  1.2G  2.5G  32% /
/dev/root       3.8G  1.2G  2.5G  32% /
devtmpfs        405M     0  405M   0% /dev
tmpfs            81M  148K   81M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           162M     0  162M   0% /run/shm
```

## 安全性設定

Cubian 的默認帳戶和密碼都是 cubie, 這很不安全.我們使用下面的命令修改密碼

```bash
$ passwd
Changing password for cubie.
(current) UNIX password: // Enter "cubie"
Enter new UNIX password: // Enter new password
Retype new UNIX password: // Retype new password
passwd: password updated successfully
```

## 預告

Cubieboard 入門筆記 下一彈： GPIO
