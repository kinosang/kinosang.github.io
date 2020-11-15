---
title: 透過Homebrew安裝node, npm update報錯
categories:
  - 應用技巧
date: 2014-01-07 22:21:05
tags:
---

今天在 Mac 上執行 nom update -g 的最後收到了下面的錯誤提示：

[caption id="attachment_1484" align="alignnone" width="300"][![nom update -g 執行出錯](/wp-content/uploads/2014/01/螢幕快照-2014-01-07-22.14.48-300x210.png)](/wp-content/uploads/2014/01/螢幕快照-2014-01-07-22.14.48-e1389272840770.png) nom update -g 執行出錯[/caption]

上網搜索了一下, 和 Homebrew 有關. (詳見[Homebrew installed npm can't upgrade itself](https://github.com/Homebrew/homebrew/issues/22408 "Homebrew installed npm can"))

<!--more-->

總結一下網上的解決方案：

```bash
$ sudo npm update -gf
$ sudo rm -rf /wp-content/local/lib/node_modules/npm
$ brew unlink node && brew link --overwrite node
```

這只是一個折中的辦法, 因為如果你這麽做, npm 本身依舊無法更新到新版本, 除非你通過Homebrew更新node.但是這個方法可以允許你正常更新其他模組而不出現錯誤.
