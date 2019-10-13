---
title: VB實現圖象在資料庫的存儲與顯示
id: 768
categories:
  - 軟體開發
date: 2010-07-03 15:10:17
tags:
---

在舉例之前, 先把數據綁定功能簡要的說明一下, 凡是具有 DataSource屬性的控件都是對數據敏感的, 它們都能通過數據控件直接使用數據庫裏的數據.比如CheckBox Control , ComboBox Comtrol , TextBox Comtrol , PictureBox Control ,Image Comtrol … 因為這種方式涉及到的知識點比較少, 也比較容易理解, 不多作說明, 現直接介紹編程步驟.

1, 從數據庫中顯示所需要的圖片

首先, 添加一個Data數據控件,設置它的DatabaseName和RecordSource屬性,

<!--more-->

```
strPath = App.Path
If Right(strPath, 1) <> "" Then
strPath = strPath & ""
MyData.DatabaseName = strPath & "ExampleDB.mdb" '數據庫存地址
MyData.RecordSource = "Info" '表名
```

第二步, 添加Image控件用來顯示圖片, 設置它的DataSource和DataField屬性.例如本例中: Image1.DataSource="MyData"和Image1.DataField=" MyPhoto" .然後設置其它具有數據綁定功能的控件用來顯示所要的其它內容, 經過這兩步的操作, 運行程序就可以顯示你要的數據了.

2, 向數據庫中添加需要存儲的圖片

首先, 利用數據控件所具有的AddNew屬性, 添加一個按鈕, 雙擊後添加如下代碼 MyData.Recordset.AddNew

第二步, 為Image控件圖片指定圖片路徑Image1.Picture = LoadPicture("圖片路徑"), 經過這兩步的操作, 就可以向數據庫中添加圖片了.

這種方法最簡單快捷, 要寫的代碼量很少.但是這種方法在運行速度和靈活性方面有一定的限制, 適合於初學者和一些簡單的應用, 要想靈活多變的顯示圖象, 下面介紹的方法或許更適應您的要求.

利用編寫代碼實現圖片的存儲與顯示

這種方法相對於方法一來說, 代碼量大, 但是它操作靈活, 能夠滿足多樣形式下的操作, 受到更多編程者的青睞.但是涉及到的知識面相對要多一些, 不僅要掌握數據庫的操作方法, 還要二進制文件的讀寫作進一步的瞭解.關於數據庫及二進制文件的基本操作很多參考書上都介紹的比較詳細, 需要時請查閱即可.在編程之前把本部分用到的變數說明如下：

```
Dim RS As New ADODB.Recordset
Dim Chunk() As Byte
Const ChunkSize As Integer = 2384
Dim DataFile As Integer, Chunks, Fragment As Integer
Dim MediaTemp As String
Dim lngOffset, lngTotalSize As Long
Dim i As Integer
```

1, 從數據庫中顯示所需要的圖片

第一步首先打開數據庫, 看有沒有要查找的內容, 有則繼續執行, 沒有就退出

```
RS.Source = "select * from Info Where Name='" & sparaName &"';"
RS.ActiveConnection = "UID=;PWD=;DSN=TestDB;"
RS.Open
If RS.EOF Then RS.cCose : Exit Sub
```

第二步, 讀出長二進制數據即圖片數據, 把它轉換成圖片文件, 操作過程如下

```
MediaTemp = strPath & "picturetemp.tmp"
DataFile = 1
Open MediaTemp For Binary Access Write As DataFile
lngTotalSize = RS!MyPhoto.ActualSize
Chunks = lngTotalSize ChunkSize
Fragment = lngTotalSize Mod ChunkSize
ReDim Chunk(Fragment)
Chunk() = RS!MyPhoto.GetChunk(Fragment)
Put DataFile, , Chunk()
For i = 1 To Chunks
ReDim Chunk(ChunkSize)
Chunk() = RS!MyPhoto.GetChunk(ChunkSize)
Put DataFile, , Chunk()
Next i
Close DataFile
```

第三步, 關閉數據庫, 這樣就可以顯示所要的圖片了.

```
RS.Close
If MediaTemp = "" Then Exit Sub
Picture1.Picture = LoadPicture(MediaTemp)
If Picture1.Picture = 0 Then Exit Subj
```

2, 向數據庫中添加需要存儲的圖片

向數據庫添加存儲的圖片是顯示圖片逆過程, 只要掌握了顯示圖片的操作, 存儲圖片的操作也就迎刃而解了, 下面將操作步驟介紹如下

第一步首先打開數據庫, 過程如下：

```
RS.Source = "select * from Info ;"
RS.CursorType = adOpenKeyset
RS.LockType = adLockOptimistic
RS.ActiveConnection = "UID=;PWD=;DSN=TestDB;"
RS.Open
```

第二步, 把要存儲的圖片轉換成二進制長文件存入數據庫中, 操作過程如下

```
RS.AddNew
DataFile = 1
Open strPathPicture For Binary Access Read As DataFile
FileLen = LOF(DataFile) ' 文件中數據長度
If FileLen = 0 Then : Close DataFile : RS.Close : Exit Sub
Chunks = FileLen ChunkSize
Fragment = FileLen Mod ChunkSize
ReDim Chunk(Fragment)
Get DataFile, , Chunk()
RS!MyPhoto.AppendChunk Chunk()
ReDim Chunk(ChunkSize)
For i = 1 To Chunks
Get DataFile, , Chunk()
RS!MyPhoto.AppendChunk Chunk()
Next i
Close DataFile
```

第三步, 更新紀錄後, 關閉數據庫, 就完成了數據圖片到數據庫的存儲.

```
RS.Update
RS.Close
Set RS = Nothing
```

兩種方法在使用方面各有所長, 讀者可以針對自己的情況做出合理的選擇.
