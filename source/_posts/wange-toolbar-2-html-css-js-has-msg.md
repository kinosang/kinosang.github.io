---
title: 仿製萬戈工具條（免插件·第二版·HTML+CSS+JS·提示文本）
id: 406
categories:
  - 軟體開發
date: 2011-02-14 12:07:02
tags:
---

不知道萬戈工具條的朋友可以到萬戈的博客《萬戈牌工具條》 認識一下這個工具條插件.
複製粘貼與替換的簡單過程

以下代碼放到style.css

<!--more-->

```
/* TopToolbar */
#topToolBar{width:100%;height:30px;position:fixed;background:#000;color:#FFFF00; z-index:9999;overflow:hidden;opacity:0.7;filter:alpha(opacity=70);-moz-opacity:.7;border-radius:0 0 5px 5px;box-shadow:0px 5px 5px #565656;-moz-border-radius:0 0 5px 5px;-webkit-border-radius:0 0 5px 5px;-khtml-border-radius:0 0 5px 5px;-webkit-box-shadow:0px 5px 5px #565656;-moz-box-shadow:0px 5px 5px #565656;top:0;_position:absolute;_top:expression(0+((e=document.documentElement.scrollTop)?e:document.body.scrollTop)+"px");}
#topToolBar a{text-decoration:none;}
#topSNS{float:right;padding:5px 10px 0 0;margin:0;}
#rssfeeddiv{width: 82px; height: 212px;right: 55px ;position:fixed;z-index:10000;overflow:hidden;top:expression(document.documentElement.scrollTop +"30px");VISIBILITY: hidden;}
```

用下面的代碼替換body標籤（在header.php中）, 其中oncopy事件你可以根據需要進行修改, Kinosang在這裏提醒您：firefox中不支持JS對剪貼板的set操作, 常見的添加版權信息的腳本在firefox中無法運行.

```
<body onclick = "document.getElementById('rssfeeddiv').style.visibility = 'hidden'; document.getElementById('topInfo').innerHTML = 'Kinosang推荐您使用Firefox3.6+, Opera10+或Safari5.0+ 在1280*720+分辨率下浏览本站.'; " oncopy = "document.getElementById('topInfo').innerHTML = '鉴于安全性的考虑奇诺网没有关闭半角字符替换功能, 如果您拷贝的是程序代码, 你需要手动把代码中的全角单双引号等字符替换成半角字符.';">
```

以下代碼放到header.php適當位置（Kinosang's Labs建議您放在的下一行）

```
<!-- RSS Feed BEGIN -->
<div id="rssfeeddiv">
<map name="FPMap0"> <area coords="0, 0, 80, 20" shape="rect" href="http://fusion.google.com/add?feedurl=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 20, 80, 40" shape="rect" href="http://reader.youdao.com/#url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 40, 80, 60" shape="rect" href="http://www.xianguo.com/subscribe.php?url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 60, 80, 80" shape="rect" href="http://www.zhuaxia.com/add_channel.php?url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 80, 80, 100" shape="rect" href="http://add.my.yahoo.com/rss?url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 100, 80, 120" shape="rect" href="http://www.newsgator.com/ngs/subscriber/subfext.aspx?url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 120, 80, 140" shape="rect" href="http://www.bloglines.com/sub/<?php bloginfo('rss2_url'); ?>" target="_blank" /> <area coords="0, 140, 80, 160" shape="rect" href="http://inezha.com/add?url=<?php bloginfo('rss2_url'); ?>" target="_blank" /> </map>![](/wp-content/themes/whitelove/images/readers.gif)
</div>
<!-- RSS Feed END -->
<!-- Top Toolbar Begin -->
<div id="topToolBar">
<div id="topInfo">Kinosang推薦您使用Firefox3.6+, Opera10+或Safari5.0+ 在1280*720+分辨率下瀏覽本站.</div>
<div id="topRight">
[![](/wp-content/themes/whitelove/images/fav.png)](javascript:void(0) "加入收藏")[![](/wp-content/themes/whitelove/images/mail.png)](mailto:chinvo@chinvo.org "E-mail")[![](/wp-content/themes/whitelove/images/rss.png)](<?php bloginfo( "訂閱本站")

[![](https://blog.7in0.me/wp-content/uploads/2015/06/479ec3a475294205ce35aa7abc762dce.gif)](http://www.jiathis.com/share/)<script charset="utf-8" type="mce-text/javascript" src="http://v2.jiathis.com/code_mini/jia.js"></script>

</div>
</div>
<!-- Top Toolbar End -->
```

記得修改上面的代碼中的Kinosang's Labs獨有的信息（包括域名, 主題名, 郵箱等）

<del>如果需要Kinosang's Labs的圖標與圖片, 你可以直接依照代碼中的img標籤下載（但不要直接使用Kinosang's Labs的png文件的url, Kinosang's Labs的流量會嚴重短缺的.）</del><span style="color: #ff0000;">Kinosang's Labs改版, 源文件已被移除.</span>

當然, 你也可以修改本工具條中的內容.

各位同學可以在下面留言討論, 第一時間答覆.

發現錯誤的同學, 及時Email我, 會有獎勵喲～

您也可以Email我以付費定製工具條等.
