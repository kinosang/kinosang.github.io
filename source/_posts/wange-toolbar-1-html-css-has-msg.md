---
title: 仿製萬戈工具條（免插件·第一版·純HTML+CSS·無滾動文本）
id: 393
categories:
  - 軟體開發
date: 2011-02-12 15:44:18
tags:
---

不知道萬戈工具條的朋友可以到萬戈的博客[《萬戈牌工具條》](http://wange.im/wg-toolbar.html) 認識一下這個工具條插件.

# 複製粘貼的簡單過程

（鑒於安全性的考慮Kinosang's Labs沒有關閉半形字元替換功能, 你需要手動把全形單雙引號等字元替換成半形字元）

以下代碼放到style.css

<!--more-->

```css
/* TopToolbar */
#topToolBar{width:100%;height:30px;position:fixed;background:#000;z-index:9999;overflow:hidden;opacity:0.7;filter:alpha(opacity=70);-moz-opacity:.7;border-radius:0 0 5px 5px;box-shadow:0px 5px 5px #565656;-moz-border-radius:0 0 5px 5px;-webkit-border-radius:0 0 5px 5px;-khtml-border-radius:0 0 5px 5px;-webkit-box-shadow:0px 5px 5px #565656;-moz-box-shadow:0px 5px 5px #565656;top:0;_position:absolute;_top:expression(0+((e=document.documentElement.scrollTop)?e:document.body.scrollTop)+"px");}
#topToolBar a{text-decoration:none;}
#topSNS{float:right;padding:5px 10px 0 0;margin:0;}
```

以下代碼放到header.php適當位置（Kinosang's Labs建議您放在的下一行）

```html
<!-- Top Toolbar Begin -->
<div id="topToolBar">[![](https://blog.7in0.me/wp-content/uploads/2015/06/ebf7e62abaa2db8e332e3e4662658308.png)](javascript:void(0) "加入收藏")
[![](https://blog.7in0.me/wp-content/uploads/2015/06/b83a886a5c437ccd9ac15473fd6f1788.png)](mailto:chinvo@chinvo.org "Email")
[![](https://blog.7in0.me/wp-content/uploads/2015/06/8bb856027f758e85ddf2085c98ae2908.png)](<?php bloginfo( "訂閱本站")

[![](https://blog.7in0.me/wp-content/uploads/2015/06/479ec3a475294205ce35aa7abc762dce1.gif)](http://www.jiathis.com/share/)
<script charset="utf-8" type="text/javascript" src="http://v2.jiathis.com/code_mini/jia.js"></script>

</div>
<!-- Top Toolbar End -->
```

記得修改上面的代碼中的Kinosang's Labs獨有的信息（包括域名, 主題名, 郵箱等）

<del>如果需要Kinosang's Labs的圖標, 你可以直接依照代碼中的img標籤下載（但不要直接使用Kinosang's Labs的png文件的url, Kinosang's Labs的流量會嚴重短缺的.）</del><span style="color: #ff0000;">Kinosang's Labs改版, 源文件已被移除.</span>

這樣, 一個仿造的“萬戈工具條”就完成了.

當然, 你也可以修改本工具條中的內容.

第二版連結：[http://7in0.me/2011/02/wange-toolbar-2-html-css-js-has-msg/](http://7in0.me/2011/02/wange-toolbar-2-html-css-js-has-msg/)

各位同學可以在下面留言討論, 第一時間答覆.

發現錯誤的同學, 及時Email我, 會有獎勵喲～

您也可以Email我以付費定製工具條等.
