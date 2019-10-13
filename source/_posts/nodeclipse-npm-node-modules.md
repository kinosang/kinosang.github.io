---
title: 讓 Nodeclipse 使用 NPM 全局模組不再需要拷貝!
id: 1439
categories:
  - 軟體開發
date: 2013-12-09 09:18:01
tags:
---

最近開始學習 Node.js 程式開發, 選用了 Eclipse 的外掛組件 Nodeclipse 作為 IDE.

但是發現一個讓人鬱悶的問題——使用 NPM 添加的全局模組不被識別, 除非手工拷貝到項目的node_modules 檔案夾下, 即使設置了NODE_PATH.

Google 也沒有查詢到有用的信息, 於是抱著試試看的態度在 Preferences 中搜尋 PATH, <del>結果還真讓我給找到了</del>!

下面給出了一個解決方案, 但是與 Eclipse 無關.

<!--more-->

[caption id="attachment_1440" align="alignnone" width="290"][![Eclipse PATH 設定](/wp-content/uploads/2013/12/螢幕快照-2013-12-09-09.12.56-290x300.png)](/wp-content/uploads/2013/12/螢幕快照-2013-12-09-09.12.56-e1389272874825.png) Eclipse PATH 設定[/caption]

秘訣就是這裏

> Preferences - General - Workspace - Linked Resources

在下面設置一個新的 PATH.

按一下『NEW...』, Name 隨便填寫, Value 填寫 NPM 的 Global PATH（我這裏是 /wp-content/local/lib/node_modules ）.

好啦, 現在按一下『OK』, 回到 Eclipse 主面板, 按一下『Run as』.

<del>鏘鏘鏘鏘!完美運行!</del>

<span style="color: #ff0000;">各位朋友, 十分抱歉, 這其實是一個錯誤的方法!測試時我沒有注意到需要調用的模組已經被拷貝到項目目錄下!</span>

正確的解決方案是在每個 Node Source File 的第一個 require 之前添加下面的代碼：

```
module.paths.push("/wp-content/local/lib/node_modules");
```
