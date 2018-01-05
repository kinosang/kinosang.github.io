---
title: 記一次因手誤導致的伺服器遷移
id: 2123
categories:
  - 互聯網絡
  - 應用技巧
date: 2015-11-27 15:00:19
tags:
---

今天對伺服器進行例行維護的時候，剛剛完成了文件的上載，順手執行了

<pre class="lang:default decode:true ">sudo chown www-data:www-data ./ -R</pre>

雖然看起來沒什麼問題，但是因為失誤，少打了一個 點 號，於是變成了

<pre class="lang:default decode:true">sudo chown www-data:www-data / -R</pre>

<!--more-->敲下 return 之後，我感覺執行時間明顯比平時要長，看了一眼命令，迅速 ctrl + c 中斷了，但是已經造成了不可挽回的損失——當我試圖執行 sudo 恢復幾個重要位置的所有者為 root 時，得到如下錯誤：

<pre class="lang:default decode:true ">sudo must be owned by uid 0 and have the setuid bit set</pre>

在 Google 搜尋得知必須把 sudo 的 owner 設置為 uid 0，然而因為平日我使用 rootless 權限的用戶，而 Ubuntu 下，root 用戶默認根本沒有設置密碼，包括 su 在內的切換到 root 身份的可能性全部斷絕，因此不得不重灌系統。

在重灌之前，使用 tar 對包括 nginx 設定（/etc/nginx）、 mysql 數據文件（/var/lib/mysql）、網站文件（/usr/share/nginx）在內的多個檔案夾進行壓縮然後藉助 dropbox-uploader 上載到 dropbox，然後在另一家 IDC 新開了一臺 VPS，把備份覆蓋到相應位置，全部網站完美復活！