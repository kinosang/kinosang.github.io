---
title: ASP留言板教學
id: 367
categories:
  - 互聯網絡
  - 軟件開發
date: 2011-02-09 12:37:37
tags:
---

我著實不喜歡asp，但是Kinosang還是寫了這篇文章，這篇文章是應一個網友的要求寫的，過一段時間，我會寫一個PHP版本的教程。

我最初接觸的動態頁面腳本就是asp，因為我學會的第一門編程語言是vb，借助它們的相似性，我進入了動態頁面開發的領域。對於動態頁面來說，PHP應該是一個更好的選擇，豐富的第三方庫讓你的開發更容易，有些人說jsp執行時更加高效，但是Kinosang認為jsp在開發過程中不及php和asp方便，這也是為什麼Kinosang遲遲沒有學會3P（別想歪了，是asp、php、jsp）的原因。

<!--more-->

ASP基本結構

```
&lt;%
語句
……
%&gt;
```

定義變數dim語句

```
&lt;%
dim a,b
a=10
b="ok!"
%&gt;
```

注意：定義的變數可以是數值型，也可以是字元或者其他類型的

簡單的控制流程語句

1.if語句（elseif和與其搭配的語句2可以沒有，也可以有多個）

```
If 條件1 then
語句1
elseif 條件2 then
語句2
else
語句3
end if
```

2.while語句

```
while 條件
語句
wend
```

3.for語句

```
for count=1 to n step m
語句1
exit for
語句2
next
```

二.ASP數據庫簡單操作教程

Kinosang在這裏聲明：您需要先創建一個數據庫，數據庫結構請依照您自己的設計建立，這裏以access為例。

.數據庫連接(用來單獨編制連接文件conn.asp)

```
&lt;%
Set conn = Server.CreateObject("ADODB.Connection")
conn.Open "DRIVER={Microsoft Access Driver (*.mdb)}; DBQ=" &amp; Server.MapPath("bbsdb1user.mdb")
%&gt;
```

(用來連接bbsdb1目錄下的user.mdb數據庫)

顯示數據庫記錄
原理：將數據庫中的記錄一一顯示到客戶端瀏覽器，依次讀出數據庫中的每一條記錄

如果是從頭到尾：用循環並判斷指針是否到末 使用： not rs.eof

如果是從尾到頭：用循環並判斷指針是否到開始 使用：not rs.bof

```
<!--#include file=conn.asp--><!-- 包含conn.asp用來打開bbsdb1目錄下的user.mdb數據庫 -->
&lt;%
set rs=server.CreateObject("adodb.recordset") ' 建立recordset對象
sqlstr="select * from message" 'message為數據庫中的一個數據表，即你要顯示的數據所存放的數據表
rs.open sqlstr,conn,1,3 '表示打開數據庫的方式
rs.movefirst '將指針移到第一條記錄
while not rs.eof '判斷指針是否到末尾
response.write(rs("name")) '顯示數據表message中的name欄位
rs.movenext '將指針移動到下一條記錄
wend '循環結束
'----Kinosang.ws分割線-----------------------------
rs.close
conn.close '這幾句是用來關閉數據庫
set rs=nothing
set conn=nothing
'----Kinosang.ws分割線-----------------------------
%&gt;
```

其中response對象是服務器向客戶端瀏覽器發送的信息

增加數據庫記錄

增加數據庫記錄用到rs.addnew,rs.update兩個函數

```
<!--#include file=conn.asp--> <!-- 包含conn.asp用來打開bbsdb1目錄下的user.mdb數據庫 -->
&lt;%
set rs=server.CreateObject("adodb.recordset") '建立recordset對象
sqlstr="select * from message" 'message為數據庫中的一個數據表，即你要顯示的數據所存放的數據表
rs.open sqlstr,conn,1,3 '表示打開數據庫的方式
rs.addnew '新增加一條記錄
rs("name")="xx" '將xx的值傳給name欄位
rs.update '刷新數據庫
'----Kinosang.ws分割線-----------------------------
rs.close
conn.close '這幾句是用來關閉數據庫
set rs=nothing
set conn=nothing
'----Kinosang.ws分割線----------------------------
%&gt;
```

刪除一條記錄

刪除數據庫記錄主要用到rs.delete,rs.update

```
<!--#include file=conn.asp--> <!-- 包含conn.asp用來打開bbsdb1目錄下的user.mdb數據庫 -->
&lt;%
dim name
name="xx"
set rs=server.CreateObject("adodb.recordset") '建立recordset對象
sqlstr="select * from message" 'message為數據庫中的一個數據表，即你要顯示的數據所存放的數據表
rs.open sqlstr,conn,1,3 '表示打開數據庫的方式
'----Kinosang.ws分割線-----------------------------
while not rs.eof
if rs.("name")=name then
rs.delete
rs.update '查詢數據表中的name欄位的值是否等於變數name的值"xx"，如果符合就執行刪除
else '否則繼續查詢,直到指針到末尾為止
rs.movenext
emd if
wend
'--Kinosang.ws分割線-----------------------------
rs.close
conn.close '這幾句是用來關閉數據庫
set rs=nothing
set conn=nothing
%&gt;
```

關於數據庫的查詢

(a) 查詢欄位為字元型

```
&lt;%
dim user,pass,qq,mail,message
user=request.Form("user")
pass=request.Form("pass")
qq=request.Form("qq")
mail=request.Form("mail")
message=request.Form("message")
if trim(user)&amp;"x"="x" or trim(pass)&amp;"x"="x" then '檢測user值和pass值是否為空，可以檢測到空格
response.write("註冊信息不能為空")
else
set rs=server.CreateObject("adodb.recordset")
sqlstr="select * from user where user=''"&amp;user&amp;"''" '查詢user數據表中的user欄位其中user欄位為字元型
rs.open sqlstr,conn,1,3
if rs.eof then
rs.addnew
rs("user")=user
rs("pass")=pass
rs("qq")=qq
rs("mail")=mail
rs("message")=message
rs.update
rs.close
conn.close
set rs=nothing
set conn=nothing
response.write("註冊成功")
end if
rs.close
conn.close
set rs=nothing
set conn=nothing
response.write("註冊重名")
%&gt;
```

(b)查詢欄位為數字型

```
&lt;%
dim num
num=request.Form("num")
set rs=server.CreateObject("adodb.recordset")
sqlstr="select * from message where id="&amp;num '查詢message數據表中id欄位的值是否與num相等，其中id為數字型
rs.open sqlstr,conn,1,3
if not rs.eof then
rs.delete
rs.update
rs.close
conn.close
set rs=nothing
set conn=nothing
response.write("刪除成功")
end if
rs.close
conn.close
set rs=nothing
set conn=nothing
response.write("刪除失敗")
%&gt;
```

幾個簡單的asp對象的講解 本文來自Kinosang.ws

response對象:服務器端向客戶端發送的信息對象,包括直接發送信息給瀏覽器,重新定向URL，或設置cookie值

request對象:客戶端向服務器提出的請求

session對象:作為一個全局變數，在整個站點都生效

server對象：提供對服務器上方法和屬性的訪問

(a) response對象的一般使用方法

比如：

```
&lt;%
resposne.write("hello, welcome to Kinosang!")
%&gt;
```

在客戶端瀏覽器就會看到 hello, welcome to Kinosang! 這一段文字

```
&lt;%
response.Redirect("www.kinosang.ws")
%&gt;
```

如果執行這一段，則瀏覽器就會自動連接到 "Kinosang's Labs" 的網址

關於response對象的用法還有很多，大家可以研究研究request對象的一般使用方法

客戶端向服務器提出的請求就是通過request對象來傳遞的

列如 ：你在申請郵箱的所填寫的個人信息就是通過該對象來將你所填寫的信息傳遞給服務器的

這是一段Kinosang給出的表單的代碼，這是提供給客戶填寫信息的，填寫完了按"提交"傳遞給request.asp文件處理後再存入服務器數據庫

```
<form action="request.asp" method="post" name="form1"><input type="text" name="user" /> <input type="text" name="pass" /> <input type="submit" name="Submit" value="提交" /></form>
```

那麼request.asp該如何將其中的信息讀入，在寫入數據庫，在這裏就要用到request對象了

下面我們就來分析request.asp的寫法

```
&lt;%
dim name,password '定義user和password兩個變數
name=request.form("user") '將表單中的user信息傳給變數name
password=request.form("pass") '將表單中的pass信息傳給變數password
%&gt;
```

通過以上的幾句代碼我們就將表單中的數據讀進來了，接下來我們要做的就是將信息寫入數據庫了，寫入數據庫的方法上面都介紹了，這裏就不一一重複了。

讀完上面的文章，你可能會問："留言板的教程在哪兒？"

其實，在閱讀本文的過程中，你已經學會了如何寫一個簡易留言板，加上你自己的想像力，去開發自己的功能更強大的留言板吧！

有什麼問題可以在下面留言討論。