---
title: 從 Wunderlist 到 OmniFocus（另：純 AppleScript 實現 OmniFocus 匯入 CSV）
id: 1853
categories:
  - 應用技巧
date: 2014-05-12 19:41:00
tags:
---

本來在下用 Wunderlist 用的好好地，一時腦子被門夾了，想要用 OmniFocus（當然，目前是 Cracked 版本）。

但是 Wunderlist 裡面如此多的條目不方便逐一搬運。

於是想到用 applescript 來實現這個過程。

首先我們從 Wunderlist WebApp 匯出數據（JSON）。

<!--more-->

進入 Wunderlist WebApp，點擊左上角的頭像，選擇“賬號設定”，可以看到“建立備份”選項。

然後，用 Node.js 取得 Task 列：

<pre class="prettyprint linenums">
$ node
> var fs = require('fs');
> var file = '/path/to/your.json';
> fs.readFile(file, 'utf8', function (err, data) {data = JSON.parse(data);fs.writeFile(file, JSON.stringify(data.tasks));});
</pre>

接著，藉助 [JSON to CSV](http://www.json2csv.com) 將 JSON 轉換成 CSV。

下一步，你還需要用 Numbers 或 Excel for Mac 編輯它，將它變成下面的格式：

> Task ID | Type | Task | Project | Context | Start Date | Due Date | Completion Date | Duration | Flagged | Notes

分別對應：

<pre class="prettyprint linenums">
Task ID ﹣> 留空
Type ﹣> ＝IF(ISBLANK(D2),"Project","Action")
Task ﹣> title
Project ﹣> 如果當前條是子任務，則填寫主任務的title
Context ﹣> 留空
Start Date ﹣> created_at
Due Date ﹣> 留空
Completion Date ﹣> completed_at
Duration ﹣> 留空
Flagged ﹣> starred
Notes ﹣> 留空
</pre>

P.S. Wunderlist 匯出的 Date 數據格式是 2014-04-27T01:57:45Z 的形式，請自行尋找工具轉換成 DD/MM/YYYY H:i:s 的格式。

最後，用下面的 AppleScript 匯入CSV 到 OmniFocus。

[download id="3"]