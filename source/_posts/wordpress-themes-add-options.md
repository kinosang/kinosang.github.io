---
title: 為wp主題添加管理選項
id: 460
categories:
  - 軟體開發
date: 2011-02-17 20:59:09
tags:
---

想必不少朋友都看到了萬戈的[《為 WordPress 插件添加後臺控制選項》](http://wange.im/control-panel-for-wordpress-plugin.html), 那麼, 如何給主題添加後臺選項呢?
下面以為主題添加“公告”功能為例進行講解, 其他項目類似.

<!--more-->

後臺處理：

首先, 我們要修改 function.php, Kinosang's Labs的處理包括 3 大塊： 獲取選項, 初始化, 標籤頁操作界面. Kinosang在這裏只創建一個公告欄, 包括兩個選項 (是否顯示公告欄和公告欄內容).如果要添加更多選項, 也只需要代碼中 3 個 TODO 的位置上追加一些代碼而已.當然, 你還需要改一下選項名稱, 將 Classic的相關內容全部換掉.
Kinosang已經在代碼中標註了大量註釋, 下面看看代碼吧.

```php
/**
* 選項組類型
*/
class ClassicOptions {
/* -- 獲取選項組 -- */
function getOptions() {
// 在數據庫中獲取選項組
$ options = get_option('classic_options');
// 如果數據庫中不存在該選項組, 設定這些選項的默認值, 並將它們插入數據庫
if (!is_array($ options)) {
$ options['notice'] = false;
$ options['notice_content'] = '';
// TODO: 在這裏追加其他選項
update_option('classic_options', $ options);
}
// 返回選項組
return $ options;
}

/* -- 初始化 -- */
function init() {
// 如果是 POST 提交數據, 對數據進行限制, 並更新到數據庫
if(isset($ _POST['classic_save'])) {
// 獲取選項組, 因為有可能只修改部分選項, 所以先整個拿下來再進行更改
$ options = ClassicOptions::getOptions();

// 數據限制
if ($ _POST['notice']) {
$ options['notice'] = (bool)true;
} else {
$ options['notice'] = (bool)false;
}
$ options['notice_content'] = stripslashes($ _POST['notice_content']);

// TODO: 在這追加其他選項的限制處理

// 更新數據
update_option('classic_options', $ options);

// 否則, 重新獲取選項組, 也就是對數據進行初始化
} else {
ClassicOptions::getOptions();
}

// 在後臺 Design 頁面追加一個標籤頁, 叫 Current Theme Options
add_theme_page("Current Theme Options", "Current Theme Options", 'edit_themes', basename(__FILE__), array('ClassicOptions', 'display'));
}

/* -- 標籤頁 -- */
function display() {
$ options = ClassicOptions::getOptions();
?>
<form id="classic_form" action="#" enctype="multipart/form-data" method="post" name="classic_form">
<div>

##

<!-- 公告欄 -->
<label> <input type="checkbox" checked="checked" name="notice" value="checkbox" /> /> <?php _e('Show notice.', 'classic'); ?> </label><label> <textarea id="notice_content" style="width: 98%; font-size: 12px;" cols="50" name="notice_content" rows="10"><;?php echo($ options['notice_content']); ?></textarea> </label>
<table>
<tbody>
<tr valign="top">
<th scope="row"><?php _e('Notice', 'classic'); ?> <small style="font-weight: normal;"><?php _e('HTML enabled', 'classic') ?></small></th>
<!-- 是否顯示公告欄 --> <!-- 公告欄內容 --></tr>
</tbody>
</table>
<!-- TODO: 在這裏追加其他選項內容 --> <!-- 提交按鈕 --> <input type="submit" name="classic_save" value="<?php _e('Update Options »', 'classic'); ?>" />

</div>
</form>
```

前臺處理：

要公告欄在首頁上顯示, 需要修改一下 index.php, 這個比較簡單, 只是通過一些判斷語句決定東西要不要顯示出來而已.當然, 你可以進行其他操作, 關鍵是獲取到選項的值, 並對它們進行處理.
其實可以分為兩步：
1\. 獲取選項 (對每個 PHP 文件, 獲取一次就行了, 可以在文件頂部執行)
2\. 對選項進行處理 (這裏判斷成立的話就將公告內容顯示出來)

```php
<!-- 獲取選項 -->
<?php $ options = get_option('classic_options'); ?>

<!-- 如果用戶選擇顯示公告欄, 並且公告欄有內容, 則顯示出來 -->
<?php if($ options['notice'] && $ options['notice_content']) : ?>
<div id="notice"></div>
```

好了, 這個教程就到這裏了.
