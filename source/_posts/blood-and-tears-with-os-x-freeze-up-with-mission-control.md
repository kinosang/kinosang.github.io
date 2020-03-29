---
title: OS X 血淚史 之 10.9 Mission Control 卡頓
id: 1709
categories:
  - 應用技巧
date: 2014-01-21 00:32:38
tags:
---

昨天在下一整天沒有上網, 因為在下在折騰 OS X.

不得不說 OS X 是個好東西, 但是還是禁不住在下的折騰啊【遠目】.

昨天遇到的問題是突然發現桌面切換卡頓.這可把我嚇壞了, 一開始以為是顯示卡故障, 於是用 AHT 進行了一次深度檢測, 就是開機過程中按住 D 鍵不放知道進入 Apple Hardware Test 界面.

[caption id="attachment_1710" align="alignnone" width="225"][![AHT 語言選擇界面](/wp-content/uploads/2014/01/IMG_20140120_150055-225x300.jpg)](/wp-content/uploads/2014/01/IMG_20140120_150055-e1390235661918.jpg) AHT 語言選擇界面[/caption]

<!--more-->

開始檢測

[caption id="attachment_1712" align="alignnone" width="300"][![AHT 工作中](/wp-content/uploads/2014/01/IMG_20140120_150200-300x225.jpg)](/wp-content/uploads/2014/01/IMG_20140120_150200-e1390235615851.jpg) AHT 工作中[/caption]

萬幸的是在下的硬件看起來沒有任何問題

[caption id="attachment_1711" align="alignnone" width="300"][![AHT 通過檢測](/wp-content/uploads/2014/01/IMG_20140120_150130-300x225.jpg)](/wp-content/uploads/2014/01/IMG_20140120_150130-e1390236392413.jpg) AHT 通過檢測[/caption]

然後, 在下腦殘了——沒錯, 就是腦殘了, 在下決定重灌 OS X, 然後通過開機按住 Option 進入了 Recovery.

抹掉磁盤後, 在下發現一個嚴重的問題——重灌要求之前曾在 App Store 購買過 10.9, 而在下是通過其他渠道獲取到 OS X 10.9 GM2進行升級的, 於是在下悲劇了.

好在還有 Option + Command + R 的無敵組合, 但是這個聯網回復系統比較慢而且會重灌成機器預裝的 10.8.

這時候在下決定通過 Time Machine 回復之前的系統.

最後的最後——

在下在 Google 上找到了一行命令解決問題的方案：

```bash
$ sudo rm /Library/Preferences/com.apple.windowserver.plist
```

欲哭無淚啊, 這讓像在下一樣折騰一整天的情以何堪（TAT）
