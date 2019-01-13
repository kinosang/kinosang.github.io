---
title: Cubieboard 入門筆記 II 藍牙串列埠 和 DHT11
id: 1801
categories:
  - 硬體研究
date: 2014-02-07 16:04:00
tags:
---

HC-05藍牙串列埠 | DHT11 溫度感測器 | Cubieboard | Cubian

總是用 Arduino as USB2TTY 連接 Cubieboard 很不方便，正好手頭有一塊 HC-05，今天就來實驗一下藍牙串列埠的使用方法。

<!--more-->

### 設定 HC-05

首先得設定一下 HC-05。讓我們祭出 Arduino as USB2TTY 神器。

[![HC-05 和 Arduino as USB2TTY](/wp-content/uploads/2014/02/CIMG2384-300x225.jpg)](/wp-content/uploads/2014/02/CIMG2384.jpg)

連接時注意下面的對應關係：

```
HC-05    Arduino
KEY   -> 5V
RXD   -> RX
TXD   -> TX
5.0   -> 5V
3.3   -> NC
GND   -> GND
```

然後打開 Arduino IDE 的 Serial Monitor （使用 screen 也可以）

選擇 `Both NL & CR -- 38400 baud`

鍵入 “AT” 並送出，如果收到 ERROR 或者沒有迴應，稍等片刻再繼續。

[![設定 HC-05](/wp-content/uploads/2014/02/設定HC-05-300x193.png)](/wp-content/uploads/2014/02/設定HC-05.png)

```
> AT
ERROR:(0)
> AT
OK
> AT+NAME=7IN0BT
OK
> AT+NAME
+ADDR:7IN0BT
OK
> AT+ADDR?
+ADDR:****:*:******
OK
> AT+UART
+UART:38400,0,0
OK
> AT+UART=115200,1,0
OK
> AT+ROLE
+ROLE:0
OK
> AT+PSWD?
+PSWD:1234
OK
> AT+PSWD=****
OK
```

> 更多指令參見 [hc-05-at_command_set.pdf](http://robopoly.epfl.ch/files/content/sites/robopoly/files/Tutoriels/bluetooth/hc-05-at_command_set.pdf)

### 連接 HC-05 和 Cubieboard

[![Cubieboard with HC-05](/wp-content/uploads/2014/02/CIMG2380-300x225.jpg)](/wp-content/uploads/2014/02/CIMG2380.jpg)

Cubieboard 預留了四針的 TTY 連接埠，我們只需要對號入座即可。

值得注意的事，Cubieboard TTY 連接埠提供的 Vcc 是 3.3V。

因此對應關系如下：

```
HC-05    Cubieboard
KEY   -> NC
RXD   -> RX
TXD   -> TX
5.0   -> NC
3.3   -> Vcc
GND   -> GND
```

然後只需要配對連線即可。

[![藍牙配對](/wp-content/uploads/2014/02/Bluetooth-Connection-300x216.png)](/wp-content/uploads/2014/02/Bluetooth-Connection.png)

這時可以用下面的指令連線 Cubieboard

```
$ screen /dev/cu.7IN0BT-DevB 115200 -U
```

### Cubieboard 96pins 連接埠非標準解決方案

在下手頭沒有 2mm 間距的連接線，杜邦線的口徑又太大，根本無法用在 Cubieboard 上面，於是在下從單排圓孔座上面拆下來一小部分來用。

[![單排圓孔座](/wp-content/uploads/2014/02/CIMG2375-300x225.jpg)](/wp-content/uploads/2014/02/CIMG2375.jpg)

這些單獨拆下來的接口可以完美地用在 Cubieboard 上面。

[![單排圓孔座](/wp-content/uploads/2014/02/CIMG2376-300x225.jpg)](/wp-content/uploads/2014/02/CIMG2376.jpg)

### DHT11 基礎用法

TBC...