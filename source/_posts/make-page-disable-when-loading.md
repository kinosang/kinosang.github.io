---
title: 網頁加載時使頁面元素失效
id: 434
categories:
  - 應用技巧
date: 2011-02-15 15:13:35
tags:
---

各位應該看到了我的網站在加載時會出現一個層覆蓋在頁面上, 致使頁面元素失效.

這樣做的好處在於：

*可以把一些必要但影響速度的元素放在頁腳加載 (如我的頂部工具條, jQuary) , 使頁面主體部分儘快展示出來

*可以保證用戶瀏覽時所有元素已經加載完成, 不影響到用戶對頁面中各元素的正常使用.

你在使用本文提到的技巧前, 可以先把放在頁面頭部 (對於wp來說是header.php) 的一些div, js元素 (比如, 如果你用了萬戈工具條代碼版或我的頂部工具條代碼, 你就可以放在頁腳之前;對於jQuary庫和代碼, 都可以移動到頁腳但要保證jQuary庫在所有jQuary代碼前被加載) 移動到頁腳 (對於wp來說是footer.php) .

<!--more-->

首先, 在頁面頭部的前添加如下代碼：

```html
<script type="text/javascript">// <![CDATA[
function addEventHandler(target, type, func) {
if (target.addEventListener)｛
target.addEventListener(type, func, false);
｝else if (target.attachEvent)｛
target.attachEvent("on" + type, func);
｝else target["on" + type] = func;
}
function move() {
var coverDIV = document.getElementById("coverdiv");
if (coverDIV) coverDIV.style.top = document.body.scrollTop || document.documentElement.scrollTop + "px";
}
addEventHandler(window, "scroll", move);
// ]]></script>
```

之後添加一個空行, 在這裏添加：

```html
<div id="coverdiv">

<span id="waitInfo" style="font-size: 23px;">Now Loading...</span>

</div>
```

然後, 在頁腳的前一行, 添加下面的代碼：

```html
<script type="text/javascript" language="JavaScript">// <![CDATA[
document.getElementById('coverdiv').style.display = "none";
// ]]></script>
```

最後, 是樣式, wp可以添加到style.css, 普通網站放到style標籤中

```css
/* loading */
/*灰色背景覆蓋層*/
#coverdiv
{
background:#333;
color:#FFFF00;
width:100%;
height:100%;
z-index:99999;
position:absolute;
filter:Alpha(Opacity=90);/*IE*/
-moz-opacity:0.8; /*FF*/
opacity:0.8; /*CSS3,FF1.5*/
display:block;
}
#coverdiv a{color:#FF0000;}
```

歡迎各位留言討論, 發現問題請及時告訴我 (留言或E-mail)  ;-) .
