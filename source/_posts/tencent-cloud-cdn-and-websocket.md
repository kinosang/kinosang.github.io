---
title: 騰訊雲 CDN 與 WebSocket
date: 2019-01-13 20:27:35
tags:
categories:
  - 互聯網絡
---

眾所周知，騰訊雲 CDN 是支援 WebSocket 的，但是最近的一個專案中使用 SignalR 時發現在生產環境中 WebSocket 連結總是在聯通 10 秒左右自動斷開，錯誤程式碼 1006.

{% asset_img 2710675219_869255_1547358247324.png WebSocket connection closed %}

<!--more-->

{% asset_img 2710675219_869254_1547358224997.png WebSocket connection closed %}

經過與騰訊雲的工程師討論，得知：

CDN 預設的回源超時時間是10秒
回源超過 10 秒無資料會回源失敗。

也就是說 WebSocket 必須至少每 10 秒有一次互動，否則會被斷開。

同理 long polling 也受此規則的影響。

使用心跳包可以避免連結被斷開。
