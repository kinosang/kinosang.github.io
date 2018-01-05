---
title: 使用Google PageSpeed Service加速你的個人網誌吧！
id: 1191
categories:
  - 互聯網絡
  - 應用技巧
date: 2013-06-21 21:27:51
tags:
---

各位朋友在造訪本站的時候可能會發現訪問速度大幅提升，同時，網誌的網域也從裸域（kinosang.ws）301跳轉到了帶有www前綴的www.kinosang.ws。

訪問加速的小秘密自然是使用了目前十分流行的CDN伺服器加速技術，如果你使用ping指令對現在的網址進行探索，你會發現返回信息中被ping的網域是pagespeed.googlehosted.com。

沒錯，這就是由Google提供的免費CDN服務——Google PageSpeed Service的伺服器。
<!--more-->

如何使用這項服務呢？
首先，用力按[這裡](https://developers.google.com/speed/pagespeed/service?hl=zh-TW)，點擊頁面右側大大的“Sign up now”按鈕。

[caption id="attachment_1193" align="alignnone" width="300"][![Google PageSpeed 項目頁面](/wp-content/uploads/2013/06/20130621205719-300x177.jpg)](/wp-content/uploads/2013/06/20130621205719-e1389272604145.jpg) Google PageSpeed 項目頁面[/caption]

然後在下面的頁面填入你的電子郵件地址和站點URL。

[caption id="attachment_1194" align="alignnone" width="636"][![申請啟用 Google PageSpeed](/wp-content/uploads/2013/06/20130621211847-e1389272328922.jpg)](/wp-content/uploads/2013/06/20130621211847-e1389272328922.jpg) 申請啟用 Google PageSpeed[/caption]

如果你的網站符合要求，那麼，靜候半個小時，你將收到Google的電子郵件。點擊“Get started”

[caption id="attachment_1195" align="alignnone" width="300"][![Google PageSpeed Email](/wp-content/uploads/2013/06/20130621212247-e1389272442629-300x196.jpg)](/wp-content/uploads/2013/06/20130621212247-e1389272442629.jpg) Google PageSpeed Email[/caption]

然後，添加一個網域。

<dl class="wp-caption alignnone" id="attachment_1196" style="width: 310px;"><dt class="wp-caption-dt"></dt></dl>

[caption id="attachment_1196" align="alignnone" width="300"][![Google PageSpeed 添加域名](/wp-content/uploads/2013/06/20130621212347-300x103.jpg)](/wp-content/uploads/2013/06/20130621212347-e1389272458698.jpg) Google PageSpeed 添加域名[/caption]

假如域名還沒有驗證擁有權，會提示你先驗證域名。推薦使用Google網站管理員工具進行驗證。這裡不再敘述如何進行驗證。

接着，修改這個網域的設置，點擊小小的鉛筆圖標，在下面的框中填入一個直接訪問你的網站的URL。

[caption id="attachment_1197" align="alignnone" width="300"][![Google PageSpeed 添加域名-設定源](/wp-content/uploads/2013/06/20130621212717-300x109.jpg)](/wp-content/uploads/2013/06/20130621212717-e1389272471215.jpg) Google PageSpeed 添加域名-設定源[/caption]

最後，到你的DNS設置www的CNAME解析到pagespeed.googlehosted.com，同時設置裸域名的301跳轉。

[caption id="attachment_1198" align="alignnone" width="300"][![Google PageSpeed 添加域名-創建CNAME](/wp-content/uploads/2013/06/20130621212824-300x71.jpg)](/wp-content/uploads/2013/06/20130621212824.jpg) Google PageSpeed 添加域名-創建CNAME[/caption]