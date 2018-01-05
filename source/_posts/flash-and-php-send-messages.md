---
title: Flash與php通信研究系列
id: 29
categories:
  - 軟件開發
date: 2010-07-02 18:27:34
tags:
---

這裏選擇as3+php架構

在 as3裏，原來的loadVars方法已經被廢棄，而改用一系列的類來實現與後臺數據的交互：URLLoader，URLLoaderDataFormat，URLStream，URLVariables
URLLoader 類和原來的loadVars類似，是加載文本、二進制數據、xml、URL數據等信息的有力工具。URLLoader接受一個URLRequest 作為構造的參數，使用load方法來加載數據。URLLoder也可以進行監聽，這點也loadVars不同，一個簡單的示例如下：

<!--more-->

AS腳本（放到關鍵幀上）：

```
//聲明一個URLLoader
var loader:URLLoader = new URLLoader();
//監聽數載加載完成事件
loader.addEventListener(Event.COMPLETE,loaded);
//事件相應函數
function loaded(e:Event){
trace(loader.data);
//輸出返回的值
}
//加載test.php
loader.load(new URLRequest("test.php"));
```

test.php：

```
<?php
//聲明一個變數並賦給一個字元串
$ data = "I am PHP!";
//輸出這個變數給flash
echo $ data;
?>
```

從上面的示例可以看出，as3和後臺交互非常簡單，但這只是單項的交互，如果要flash向後臺發送數據並傳回數據該如何去做呢？
這個時候你可能會把代碼該成這樣：

AS腳本（放到關鍵幀上）：

```
var loader:URLLoader = new URLLoader();
loader.data={message:"hello im flash!"};
loader.addEventListener(Event.COMPLETE,loaded);
function loaded(e:Event){
trace(loader.data);
//輸出
}
loader.load(new URLRequest("test.php"));
```

test.php：

```
<?php
$ flashData = $ _POST['message'];
echo "this is flash say:$ flashData";
?>
```

看看輸出的代碼，你得到了什麼？是的，信息並沒有傳輸到php，你還是在用loadVars的方式試圖傳送數據，但as3裏已經不是這樣了， URLLoader的data只有在數據被下載完時才會被初始化，在數據沒有加載完成時，它是等於null的，也就是說，它只包含接受到的數據，而不管要發送的數據，那麼，as3裏如何向後臺發送數據呢？使用URLVariables。URLVariables允許你在flash和後臺程序間傳輸變數， as3裏已經把發送和接受數據分離，再也不像as2的loadVars那樣，一個類通吃所有了，那麼這個URLVariables如何送要發送的數據呢？
你可能找遍了URLLoader的文檔也沒有發現與它相關的東西，是的URLLoader並不和URLVariables有關聯，而是 URLRequest在使用它，URLRequest包含整個http請求的所有信息，所以我們要發送的數據要放在這裏，URLRequest有一個 data屬性他接受一個object類型的參數，這就是我們要發送的數據。同時你可能還注意到了，URLRequest也管理用什麼樣的方式來發送 http請求，它的method屬性接受一個字元參數，post或者是get。好了，現在我們來看看它們是怎麼工作的：

AS腳本（放到關鍵幀上）：

```
var loader:URLLoader = new URLLoader();
//聲明一個URLRequest
var url:URLRequest = new URLRequest("test.php");
//設置傳輸方式為POST
url.method = URLRequestMethod.POST;
//聲明一個URLVariables
var values:URLVariables = new URLVariables();
//設置要傳輸的信息
values.message="I am Flash!";
url.data = values;
loader.addEventListener(Event.COMPLETE,loaded);
function loaded(e:Event){
trace(loader.data);
//輸出
}
loader.load(url);
```

test.php同上

怎麼樣，是不是也很方便的實現了as3和後臺的數據傳輸？現在我們的程序可以雙向交互數據了，但這只是一些簡單的數據，如果你要傳輸帶有結構的數據，（熟悉as2的人都知道loadVars可以自動解析下載數據的結構），讓我們先按loadVars的方式來嘗試下，看看 URLLoader是不是能自動的解析下載的數據。

AS腳本（放到關鍵幀上）：

```
var loader:URLLoader = new URLLoader();
//聲明一個URLRequest
var url:URLRequest = new URLRequest("test.php");
//設置傳輸方式為POST
url.method = URLRequestMethod.POST;
//聲明一個URLVariables
var values:URLVariables = new URLVariables();
//設置要傳輸的信息
values.message="I am Flash!";
url.data = values;
loader.addEventListener(Event.COMPLETE,loaded);
function loaded(e:Event){
trace(loader.data);
//輸出整串數據
trace(loader.data.phpMessage);
//輸出phpMessage
trace(loader.data.flashMessage);
//輸出flashMessage
}
loader.load(url);
```

test.php：

```
<?php
$ flashData = $ _POST['message'];
$ phpMessage = "I am PHP!";
echo "phpMessage=$ phpMessage&flashMessage=$ flashData";
?>
```

可以看到，數據被完全下載下來了，但是卻沒有被解析，還拋出了異常，那麼是不是 URLLoader就不能自動解析數據了呢？不是，相反URLLoader 提供了多種數據解析方式供你選擇。這些數據解析方式都在URLLoaderDataFormat裏，他們是：BINARY-以2進制的方式解析 TEXT-以文本的方式解析 VARIABLES-以變數-值配對的方式解析。URLLoader的dataFormat屬性提供了對解析方式的選擇，好，我們再來修改下程序使它變的更完美些：

AS腳本（放到關鍵幀上）：

```
var loader:URLLoader = new URLLoader();
//聲明一個URLRequest
var url:URLRequest = new URLRequest("test.php");
//設置傳輸方式為POST
url.method = URLRequestMethod.POST;
//聲明一個URLVariables
var values:URLVariables = new URLVariables();
//設置要傳輸的信息
values.message="hello im flash!";
url.data = values;
//設置以變數-值配對的方式解析數據
loader.dataFormat = URLLoaderDataFormat.VARIABLES;
loader.addEventListener(Event.COMPLETE,loaded);
function loaded(e:Event){
trace(loader.data);
//輸出整串數據
trace(loader.data.phpMessage);
//輸出整串數據phpMessage
trace(loader.data.flashMessage);
//輸出整串數據flashMessage
}
loader.load(url);
```

test.php同上

以上這些就是as3裏和後臺交互的基礎內容，現在你可以在as3裏創建這樣的交互，獲取動態數據來豐富你的程序，另外還有一些東西值得注意：
1、捕獲和處理異常，上述的例子裏都是假定在完美的測試環境下運行，所以沒有加上異常捕獲和處理的代碼，但現實中你可能會遇到各種各樣的情況；URLLoader可能引發好幾種異常情況，詳細請參見說明文檔
你應該通過監聽ioError和securityError事件，或者通過try-catch來處理他們，即使你確信不會發生這樣的問題。
2、文件的編碼，經常很多數據變成了亂碼或者解析出錯都是因為編碼的問題，我的做法是確保發送的數據和後臺傳回的數據都是UTF-8格式（flash默認編碼為utf-8）