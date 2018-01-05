---
title: WP短代碼只評論可見AJAX版
id: 875
categories:
  - 互聯網絡
  - 應用技巧
  - 軟件開發
date: 2011-07-31 12:36:05
tags:
---

剛剛在雷鋒博客群裏看到 亂了感覺 發佈的評論可見功能短代碼（[《WP短代碼之評論可見》](http://messense.me/wp-shortcode-of-reply-to-read.html)），對於其中的“評論後刷新”頗為煩惱。

一切追求快捷！

於是就有了這篇文章和後面的代碼。

亂了感覺在原文中是這樣解釋這段代碼的：

> 今天繼續給大家分享《WP短代碼之評論可見》，這個可就實用多了吧，比如說你的文章裏面給出了一個115的附件下載連結又不希望看文章的人直接去下載了而不對文章發表評論，這時候評論可見就可以起作用了。好了，下面給出實現這個功能的代碼，加到你的主題的functions.php中。代碼雖然很短，但是我可是查閱了很多WP函數參考的資料才終於寫好的，這裏直接把演示也放進去，嘿嘿，這段代碼評論可見哦！

好了，廢話不多說，上代碼：
<!--more-->
注意：使用本代碼前提是使用Ajax評論功能！

下面的代碼放在functions.php中

[reply]

```
/**
* 短代碼之評論可見
* @author 亂了感覺(http://messense.me)
* 整理 Kinosang(http://chinvo.org)
*/
function reply_to_read($ atts,$ content=null){
extract(shortcode_atts(array("notice"=>'此處內容需要評論本文後刷新本頁才能查看.'),$ atts));
$ email=null;
$ user_ID=(int)wp_get_current_user()->ID;
if($ user_ID>0){
$ email =  get_userdata($ user_ID)->user_email; //如果用戶已登錄,從登錄信息中獲取email
}else if(isset($ _COOKIE['comment_author_email_'.COOKIEHASH])){
$ email=str_replace('%40','@',$ _COOKIE['comment_author_email_'.COOKIEHASH]); //如果用戶未登錄但電腦上有本站的Cookie信息，從Cookie裏讀取email
}else{
return $ notice; //無法獲取email，直接返回提示信息
}
if(empty($ email)){
return $ notice;
}
global $ wpdb;
$ post_id=get_the_ID(); //文章的ID
$ query="SELECT `comment_ID` FROM {$ wpdb->comments} WHERE `comment_post_ID`={$ post_id} and `comment_approved`='1' and `comment_author_email`='{$ email}' LIMIT 1";
if($ wpdb->get_results($ query)){
return $ content; //查詢到對應的已經審核通過的評論則返回內容
}else{
return $ notice; //否則返回提示信息
}
}

add_shortcode('reply', 'reply_to_read');

function ajax_post(){
if( isset($ _GET['action'])&& $ _GET['action'] == 'ajax'){
$ post_id=$ _GET['id'];
query_posts("p={$ post_id}");the_post();the_content();
die();
}else{
return;
}
}
add_action('init', 'ajax_post');
```

[/reply]

在你的comments-ajax.js中找到：

```
if ( temp && respond ) {
temp.parentNode.insertBefore(respond, temp);
temp.parentNode.removeChild(temp)
}
```

在其後插入：

[reply]

```
//handle reply-to-read,auto refresh page
if($ ('.reply-to-read').length>0){
var title=$ ('.post-info h2').text(); //這裏的選擇器改成你的文章的標題的選擇器
var postId=$ ('.post').attr('id').replace(/^post-(.*)$ /,'$ 1');
$ .ajax({
type: 'GET',
url: 'http://'+document.domain+'/?action=ajax&id='+postId,
beforeSend:function(){
$ ('.post-info h2').text('正在重新加載文章中'); //這裏的選擇器改成你的文章的標題的選擇器
$ body.animate( {scrollTop: $ ('.entry').offset().top - 200}, 900);//這裏的選擇器改成你的文章的內容的選擇器
},
success:function(data){
$ ('.post-info h2').text(title); //這裏的選擇器改成你的文章的標題的選擇器
$ ('.entry').html(data);
},
error:function(){
alert('ajax請求出錯,請重試!')
}
});
}
```

[/reply]
關於用法，原作者如是說：

> 用法很簡單，即：[@reply]評論可見的內容[@/reply](去掉@)，同時支持自定義提示信息，使用[@reply notice="自定義的提示信息"]評論可見的內容[@/reply](同樣要去掉@)。