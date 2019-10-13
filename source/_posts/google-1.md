---
title: Google +1
id: 716
categories:
  - 應用技巧
date: 2011-07-09 15:24:08
tags:
---

我不是標題黨，因此也不會像萬大叔那樣弄個[《衣服脫脫脫，越脫越精采》](http://wange.im/ifttt.html)這樣的標題。

說實話，Google+1也是在萬大叔的這篇文章中看到的，之前曾經考慮過Facebook的按鈕工具，但是鑒於國內很多網友不能正常顯示，也就沒有添加，現在，在我的博客以及後續網頁中，都可以看到Google+1按鈕了。

很多人認為Google+1按鈕只有很侷限的用法：添加一段腳本，用戶點擊，影響Google搜索結果。

根據Google的個性，應該不會這麼簡單，或許，還有更深層次的API，可以提供其他信息或功能。

通過Google Code的檢索，我找到了[關於Google+1的文檔](http://code.google.com/intl/zh-CN/apis/+1button/)。

在這篇文檔中，對於Google+1按鈕的定製等進行了很深入的講解。

<!--more-->

下面就以將Google+1按鈕融入WP博客（也可以是一般網站，那麼就要把wp的函數修改掉）“Hot”功能為例進行一下講解。

> +1 標記參數
>
> 鍵                   描述
>
> callback       在用戶點擊 +1 按鈕後被調用。回調函數可以接受包含href和state的 JSON 對象
>
> href               要進行 +1 推薦的網址

上面兩個參數就是本次要用到的東西。

首先是在文章列表中應用Google+1。

找到你的模板的home.php文件（若沒有，則修改index.php），找到

```
<?php while (have_posts()) : the_post(); ?>
```

向下查找，尋找如下代碼：

```
<div class="post" id="post-<?php the_ID(); ?>">
```

在這個div的範圍內，選擇一個你認為合適的地方，添加下面的腳本

```
<!-- Google+ Begin -->
<script type="text/javascript" src="https://apis.google.com/js/plusone.js">
{lang: 'zh-CN'}
</script>
<g:plusone size="small" href="<?php the_permalink() ?>"></g:plusone>
<!-- Google+ End -->
```

當然，script標籤你也可以移動到其他位置，重點是g:plusone標籤，它決定了Google+1按鈕的位置。
你也可以使用兼容W3C規則的標籤形式。

如法炮製，把文章歸檔 (archive.php)也修改一下。
當然，你還可以在文章頁面（single.php）的任意位置添加下面的代碼：

```
<!-- Google+ Begin -->
<script type="text/javascript" src="https://apis.google.com/js/plusone.js">
{lang: 'zh-CN'}
</script>
<g:plusone size="small"></g:plusone>
<!-- Google+ End -->
```

然後，下面就是重頭戲了。

首先，你要確認你的網站有Hot相關的數據庫，然後，你要新建一個頁面用於將數據轉存入數據庫。

（後文編寫中，精采稍後繼續……）
