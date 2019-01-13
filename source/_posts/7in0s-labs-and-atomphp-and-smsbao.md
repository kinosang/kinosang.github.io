---
title: AtomPHP And SMSbao
id: 1833
categories:
  - 軟件開發
date: 2014-03-22 21:23:00
tags:
---

很久沒有寫網誌了，今天忙裡偷閒寫一點東西。

首先是慶祝一下 AtomPHP 的第一版終於完成了。這個項目是在下寫其他東西的時候產生的副產品，完全是邊用邊修改。

這是一個微型 PHP MVC Framework。帶有一個可選的**模版**組件和靈活的附加模塊系統。詳細介紹見 README.md。

目前項目放在 [github](https://github.com/kinosang/AtomPHP) 上，歡迎大家試用。

然後，吐槽一下今天用到的一個小東西—— SMSbao 的 簡訊 API。這玩意兒簡直不能直視，直接使用 GET 方法和明文 username、password 就可以調用這些 API。設計者的腦洞貌似略大。