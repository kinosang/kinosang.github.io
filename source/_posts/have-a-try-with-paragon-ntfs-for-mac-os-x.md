---
title: 體驗 Paragon NTFS for Mac OS X
id: 1882
categories:
  - 雜物箱
date: 2014-06-03 13:49:00
tags:
---

在 Mac OS 中使用 NTFS 文件系統一直不是一件令人愉快的事情.
雖然 Mac OS 早已原生支持 NTFS 文件系統的訪問, 但是卻不支持 NTFS 文件系統的寫入.

NTFS 文件系統的磁碟在 Mac 上會表現得如同一個光盤一樣, 是只讀的.

[![NTFS on OS X](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-13.00.15-300x194.png)](/wp-content/uploads/2014/06/-2014-06-03-13.00.15-e1401773811475.png)

<!--more-->

** 一些選擇 **

有網友發現了利用一系列的 SHELL 指令另 NTFS 分區可寫的辦法, 但是對於每一個不同的分區, 每一次插拔, 都需要重新分析, 設置, 十分不方便而且容易出錯導致文件系統出現問題.

而相對方便的是使用諸如 NTFS for Mac ,  NTFS-3G 這樣的第三方插件.

NTFS-3G 是開源, 免費的, 但是稍有不足：寫入緩慢, 且會導致 NTFS 文件系統讀取速度下降.並且, NTFS-3G 還有 在每次彈出分區後, 會殘留分區的載點等問題（下圖中 “TEST”, “TEST 1”, “TEST 2”, “TEST 3” 為同一 U 盤多次插拔後殘留的檔案夾）.

[![NTFS-3G left directory on /Volumes](/wp-content/uploads/2014/06/Pasted-Graphic-1-e1401774347230.png)](/wp-content/uploads/2014/06/Pasted-Graphic-1-300x207.png)

相對於麻煩多多的命令行法和社區維護的 NTFS-3G, Paragon NTFS for Mac 作為一款成熟的商業產品, 使用便捷, 可靠, 方便.

** Paragon NTFS for Mac 的三大特性 **
* 高效 - Paragon NTFS for Mac 效率直逼 Mac OS X 原生驅動, 支援 Windows 和 Mac 之間的大量數據傳輸, 並且支援 Mac OS X Mavericks
* 便捷 - 圖形化安裝界面和控制面板, 不必記憶或操作命令行
*安全 - 提供相應的權限控制選項

** Paragon NTFS for Mac 試用 **

Paragon NTFS for Mac 提供了 10 天的免費試用版本, 大家可以到其[官網](http://www.paragon-software.com/home/ntfs-mac/)下載.

安裝過程十分簡單, 雙擊下載回來的 dmg 掛載, 然後運行 “安裝 Paragon NTFS for Mac OS X”.

[![Install NTFS for Mac](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-12.56.16-300x206.png)](/wp-content/uploads/2014/06/-2014-06-03-12.56.16-e1401774366864.png)

一路下一步（繼續）, 最後會要求重新啟動計算機.

[![Install NTFS for Mac](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-12.56.19-300x268.png)](/wp-content/uploads/2014/06/-2014-06-03-12.56.19-e1401774378980.png)

重啟之後, 會彈出 Paragon NTFS for Mac 的設置面板, 這是在提示購買註冊.不用管它, 直接關閉即可.

[![NTFS for Mac](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-13.19.12-300x258.png)](/wp-content/uploads/2014/06/-2014-06-03-13.19.12-e1401774389530.png)

這時候再插入一個 NTFS 格式的磁碟, 就會發現 Finder 大變樣了.我們拷貝一個大一點的東西進去試試效果.速度還不錯.

[![NTFS on Mac now is Writable](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-12.55.09-300x196.png)](/wp-content/uploads/2014/06/-2014-06-03-12.55.09-e1401774401593.png)

在Paragon NTFS for Mac 的設置面板還可以 開關 Paragon 驅動, 限制磁碟訪問, 清除隱私數據, 設置從 NTFS 磁碟啟動計算機.

[![NTFS for Mac](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-12.55.37-300x258.png)](/wp-content/uploads/2014/06/-2014-06-03-12.55.37-e1401774411505.png)

另外, 使用 Paragon NTFS for Mac, 還可以在磁碟工具中創建 NTFS 卷, 這一點是包括 NTFS-3G 等在內的其他解決方案所不具備的.

[![Create NTFS Filesystem with Disk Utility.app](/wp-content/uploads/2014/06/螢幕快照-2014-06-03-12.56.57-300x265.png)](/wp-content/uploads/2014/06/-2014-06-03-12.56.57-e1401774423287.png)

P.S. 安裝 Paragon NTFS for Mac 之前, 要完整卸載其他同類軟件, 包括 NTFS-3G 等.
以 NTFS-3G 舉例：
若使用 Homebrew 或 MacPorts , 直接用相關命令行卸載, 然後恢復 /sbin/mount_ntfs 的備份（若你在安裝時替換了該文件的話）.
若使用 NTFS-3G 的 dmg/pkg 包安裝, 在 NTFS-3G 的設置面板可以找到卸載選項.
