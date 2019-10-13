---
title: WordPress普及系列（二）從零開始搭建WP站點
id: 608
categories:
  - 應用技巧
date: 2011-06-05 14:52:25
tags:
---

撰寫本系列文章的主要目的，是普及WordPress博客程序，寫作之前，參考了萬大叔的[《WordPress 那些事兒》](http://wange.im/about-wordpress.html)、[《WordPress 我變我變我變變變》](http://wange.im/wordpress-is-not-everything.html)兩篇文章，下文中引用自萬大叔文章中的文本會被註明。

<span style="line-height: 1.714285714; font-size: 1rem;">上一篇博文（</span>[《WordPress普及系列（一）為什麼選擇WP》](http://www.chinvo.org/html/y_2011/m_05/593.html)<span style="line-height: 1.714285714; font-size: 1rem;">）介紹了WordPress的優勢，從這一篇開始，Kinosang開始講解WordPress的使用。</span>

想要使用WP，第一步當然是安裝，安裝方式分為本地安裝和遠程安裝，步驟基本相同，只不過遠程安裝需要你擁有一臺服務器，並將WP的文件上傳至你的服務器。

<!--more-->

這裏以本地安裝為例進行講解。

一、搭建運行環境

本地安裝需要你在本地（就是你的電腦上，別告訴我你看不懂-_-!）搭建一個PHP+Mysql運行環境，如果你已經搭建好了一個運行環境，請跳過本步。

萬戈（叫他萬大叔好像傷到了他的心）推薦在本地搭建運行環境時使用<span style="text-decoration: underline;">XAMPP for Windows PHP+MySql 套裝</span>，我個人習慣使用appserv（win32版本已在文末提供下載），這裏就以appserv-win32進行講解了。

運行“appserv-win32.exe”進行安裝，設置什麼的很簡單，不多說了，上圖：

[![](https://blog.7in0.me/wp-content/uploads/2015/06/c4ca4238a0b923820dcc509a6f75849b.png "捕獲1")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b71)

[![](https://blog.7in0.me/wp-content/uploads/2015/06/c81e728d9d4c2f636f067f89cc14862c.png "捕獲2")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b72)

[![](https://blog.7in0.me/wp-content/uploads/2015/06/eccbc87e4b5ce2fe28308fd9f2a7baf3.png "捕獲3")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b73)

*   這個地址可以修改，Kinosang's Labs在這裏保留了默認

[![](https://blog.7in0.me/wp-content/uploads/2015/06/a87ff679a2f3e71d9181a67b7542122c.png "捕獲4")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b74)

*   全勾上哦～否則會出現問題的，除非你之前安裝過MySQL等部件

[![](https://blog.7in0.me/wp-content/uploads/2015/06/e4da3b7fbbce2345d7772b0674a318d5.png "捕獲5")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b75)

*   Kinosang's Labs在這裏修改了Port（連接埠）！
*   Server Name可以隨便填，但推薦填寫127.0.0.1（僅限本地測試時）
*   Email請填寫正確的E-mail地址，這裏因為是演示，所以隨便填寫了一個

[![](https://blog.7in0.me/wp-content/uploads/2015/06/1679091c5a880faf6fb5e6087eb1b2dc1.png "捕獲6")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e6%8d%95%e8%8e%b76)

最後，安裝完成後點擊Finish即可。

二、安裝WordPress

把WP的文件解壓到appserv安裝目錄下的WWW目錄中（最好新建一個目錄，如wordpress，放在新建的目錄裏面）。

然後訪問http://127.0.0.1:8080/wordpress（以上文中所述地址）

會提示錯誤，如下圖：

[![](https://blog.7in0.me/wp-content/uploads/2015/06/6512bd43d9caa6e02c990b0a82652dca1.png "安裝1")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%851)

點擊“試試創建一個配置文件”

*   Kinosang's Labs提醒您確保wp所在目錄可寫，windows取消“只讀”，linux設置為777

進入下面的界面：

[![](https://blog.7in0.me/wp-content/uploads/2015/06/3c59dc048e8850243be8079a5c74d0791.png "安裝2")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%852)

點擊“現在就開始！”下一步：

[![](https://blog.7in0.me/wp-content/uploads/2015/06/c16a5320fa475530d9583c34fd356ef51.png "安裝3")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%853)

填寫好數據庫信息，點擊“提交”

*   Kinosang's Labs不推薦使用root級別數據庫用戶
*   Kinosang's Labs推薦將表名前綴修改為無規律的字元（與您的網站或wp差距越大越好）

進入下一步：

[![](https://blog.7in0.me/wp-content/uploads/2015/06/3416a75f4cea9109507cacd8e2f2aefc1.png "安裝4")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%854)

很好，成功連接數據庫，說明填寫的信息沒問題，否則就要修改數據庫信息。點擊“進行安裝”進入下一步。

填寫站點信息：

*   Kinosang's Labs不推薦用默認的用戶名——admin
*   Kinosang's Labs提醒您郵箱必須填寫正確的

[![](https://blog.7in0.me/wp-content/uploads/2015/06/2838023a778dfaecdc212708f721b7881.png "安裝5")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%855)

點擊“安裝WordPress”，進入下一步

[![](https://blog.7in0.me/wp-content/uploads/2015/06/7f39f8317fbdb1988ef4c628eba025911.png "安裝6")](http://www.chinvo.org/blog/chinvo/2011/06/608-propaganda-wordpress-2-start-with-nothing.html/%e5%ae%89%e8%a3%856)

於是，不要沈浸在安裝的喜悅中了，安裝已經結束了！快點擊“登錄”，登入後臺吧！
