---
title: WP根據用戶級別執行不同操作
id: 370
categories:
  - 軟體開發
date: 2011-02-09 18:43:30
tags:
---

首先要說明一點, 本站的VIP級別用戶是使用Role Manager創建的, Role Manager的具體用法我會專門講解, 下面的代碼中特殊用戶組以本站VIP用戶（level = 3）為例.

一, 對登錄或未登錄或評論者的訪客顯示不同內容

【一】對登錄用戶不顯示, 對評論者和訪客顯示的內容

```php
<?php if (!$ user_ID) { ?>
這裏填某些調用代碼, 內容
<?php } ?>
```

<!--more-->

【二】對登錄用戶和評論者不顯示, 只對訪客顯示的內容

```php
<?php if (!$ user_ID && !isset($ _COOKIE['comment_author_'.COOKIEHASH])) { ?>
這裏填某些調用代碼, 內容
<?php } ?>
```

【三】只對登錄用戶顯示的內容

```php
<?php if ($ user_ID) { ?>
這裏填某些調用代碼, 內容
<?php } ?>
```

【四】只對評論者顯示的內容

```php
<?php if (!$ user_ID && isset($ _COOKIE['comment_author_'.COOKIEHASH])) { ?>
這裏填某些調用代碼, 內容
<?php } ?>
```

二, 對不同級別的登錄者顯示不同內容

```php
<?php
//連結SQL, Kinosang's Labs提醒您請修改sql用戶名, 密碼, 使用的數據庫名
$ conn=@mysql_connect("localhost", "root","pwd");
$ db = @mysql_select_db("dbname",$ conn);

//讀取當前id對應的用戶的級別並依據級別設置顯示的內容
if ($ user_ID) {
$ result_level = @mysql_query("SELECT meta_value FROM wp_usermeta WHERE user_id = '" . $ user_ID . "' and meta_key = 'w_user_level'",$ db); //Kinosang's Labs提醒您這裏的wp_usermeta改為你的對應的表名
//分情況設定更新時間
if ($ result_level[0]->meta_value < 3) { //訂閱者, 投稿者和作者級別（訂閱者的level=0, 投稿者的level=1, 作者的level=2）
?>
這裏填某些調用代碼, 內容 Kinosang's Labs一般用戶
<?php
}
if ($ result_level[0]->meta_value == 3) { //VIP用戶（這是Kinosang's Labs自定義用戶組, wp原本沒有level=3的用戶組）
?>
這裏填某些調用代碼, 內容 Kinosang's LabsVIP用戶
<?php
if ($ result_level[0]->meta_value > 5) { //官方用戶（wp中默認 編輯的level=7, 管理員的level=10）
?>
這裏填某些調用代碼, 內容 Kinosang's Labs管理團隊
<?php
}else{
//未註冊用戶
?>
這裏填某些調用代碼, 內容 Kinosang's Labs過客
<?php ?>
```

如果你有什麼不明白的地方請留言討論 :smile:

不要試圖用代碼中的用戶名, 密碼等信息攻擊本站, Kinosang不會傻到不修改這些隱私信息就發佈文章.
