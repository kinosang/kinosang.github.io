---
title: 在 Azure 上使用虛擬機器部署 Web 服務 以WordPress 爲例
id: 2377
categories:
  - 雜物箱
date: 2016-10-14 15:31:10
tags:
---

### 要點

*   建立虛擬機器（資源管理員部署模型）
*   網路安全性群組
*   Linux 基礎操作（不在本文詳細介紹）

<!--more-->

### 通過 Azure 入口網站建立虛擬機器

造訪 [Azure 入口網站](https://portal.azure.com/)，使用 Microsoft Account 登入。可以看到新版 Azure 入口網站。

![azure-portal-dashboard](https://blog.7in0.me/wp-content/uploads/2016/10/Azure-Portal-Dashboard.jpg)

### 建立虛擬機器

按一下左側“新增”按鈕，在展開的面板上選擇“計算”。

![create-new-computing-instance-on-azure.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-computing-instance-on-azure.jpg)

我們選擇“精選應用程式”中的“Ubuntu Server 16.04 LTS”並在展開的面板上按一下“建立”。注意“選取部署模型”爲“資源管理員”。

![create-new-ubuntu-server016.04-instance.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance.png)

填寫虛擬機器名稱，設定使用者名稱，提供一個 SSH 公開金鑰 或 密碼，填寫新建資源群組名稱，選擇部署位置，按一下“確定”進入下一步。（下圖以 DEMO/chino 爲例且未提供 SSH金鑰）

![create-new-ubuntu-server016.04-instance-step-one.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-one.png)

我們選取 DS1 標準 型虛擬機器，這是最小可用型號，可以按一下“檢視全部”查看所有可用大小。

![create-new-ubuntu-server016.04-instance-step-two.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-two.png)

在第三步，我們只需要修改“網路安全型群組”一項。

![create-new-ubuntu-server016.04-instance-step-three.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-three.png)

按一下“加入輸入規則”新增一條連入規則，如圖，我們允許 HTTP （80埠）連入，按“確定”新增。

![create-new-ubuntu-server016.04-instance-step-four.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-four.png)

之後再爲安全性群組命名並按“確定”。

![create-new-ubuntu-server016.04-instance-step-five.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-five.png)

回到“設定”按“確定”完成工作。

![create-new-ubuntu-server016.04-instance-step-six.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-six.png)

檢視摘要並按“確定”建立虛擬機器。

![create-new-ubuntu-server016.04-instance-step-seven.png](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server016.04-instance-step-seven.png)

等待部署完成。

![create-new-ubuntu-server16.04-instance-pending.jpeg](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server16.04-instance-pending.jpeg)

部署完成後可查看虛擬機器的共用 IP 位址（若未出現，按一下儀表板上的“DEMO”）

![create-new-ubuntu-server16-04-instance-done](https://blog.7in0.me/wp-content/uploads/2016/10/create-new-ubuntu-server16.04-instance-done.png)

### 通過 Azure 入口網站調整網路安全性群組

按一下左側的“資源群組”按鈕展開“資源群組”面板，點選剛剛建立的“DEMO”

![modify-nsg-step-one](https://blog.7in0.me/wp-content/uploads/2016/10/modify-nsg-step-one.png)

點選網路安全性群組 demo-nsg展開“網路安全性群組”面板，在左側“連入安全性規則”和“連出安全性規則”中可以修改規則。

![modify-nsg-step-two](https://blog.7in0.me/wp-content/uploads/2016/10/modify-nsg-step-two.png)

### 安裝 WordPress 運行環境

通過 ssh 登入你的虛擬機器：

<pre>$ssh user@public-ip</pre>

例如：

<pre>$ ssh chino@your-ip-here</pre>

![connect-to-server-via-ssh.png](https://blog.7in0.me/wp-content/uploads/2016/10/connect-to-server-via-ssh.png)

打“yes”按確認鍵繼續，遠端伺服器可能會要求密碼，注意 Linux 下輸入密碼沒有視覺反饋，如果此前選擇的“SSH 公開金鑰”，則需要透過 ssh-agent 和 ssh-add 正確添加 SSH 私人密鑰。

首先安裝 WordPress 運行環境，這裏選擇 Apache2 + MySQL + PHP7

<pre>$ sudo apt update &amp;&amp; sudo apt -y upgrade
$ sudo apt -y install apache2 mysql-server php7.0 libapache2-mod-php7.0 php7.0-mysql php7.0-mcrypt</pre>

若此前選擇的作業系統爲“Ubuntu 12.04”或“Ubuntu 14.04”，則第二條指令修改爲

<pre>$ sudo apt -y install libapache2-mod-php5 mysql-server php5-mysql php5-mcrypt</pre>

安裝 MySQL 過程中要求設定 root 密碼

![螢幕快照 2016-10-14 14.51.49.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-14.51.49.png)![螢幕快照 2016-10-14 14.52.20.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-14.52.20.png)

<pre>$ sudo a2enmod php7.0 #手動啓用 php7 for apache
$ sudo a2dismod mpm_event #禁用 MPM，否則與 php7 NTS 衝突
$ sudo a2enmod mpm_prefork
$ sudo systemctl restart apache2</pre>

若爲“Ubuntu 12.04”或“Ubuntu 14.04”則上面四條指令修改爲

<pre>$ sudo service apache2 restart</pre>

### 安裝 WordPress

<pre>$ wget https://wordpress.org/latest.tar.gz
$ tar vzxf latest.tar.gz</pre>

![螢幕快照 2016-10-14 14.57.50.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-14.57.50.png)

<pre>$ cd /var/www/html #前往 Apache2 根目錄
$ sudo rm index.html #刪除默認主頁
$ sudo mv ~/wordpress/* . # 移動 WordPress 到當前目錄
$ sudo chown www-data:www-data ./ -R
$ ls #查看當前目錄下的檔案</pre>

![螢幕快照 2016-10-14 15.01.02.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.01.02.png)

然後爲 WordPress 建立 MySQL 用戶

<pre>$ mysql -uroot -p #登入 MySQL 控制檯，會要求輸入此前設置的 MySQL root 密碼
mysql&gt; CREATE USER 'demo'@'localhost' IDENTIFIED BY 'password';
mysql&gt; CREATE DATABASE demo;
mysql&gt; GRANT ALL PRIVILEGES ON demo.* TO 'demo'@'localhost';
mysql&gt; FLUSH PRIVILEGES;
mysql&gt; exit</pre>

打開瀏覽器（比如 Google Chrome），轉到 http://your-ip-here 根據頁面提示繼續安裝。

![螢幕快照 2016-10-14 15.21.11.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.21.11.png)

![螢幕快照 2016-10-14 15.21.31.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.21.31.png)

![螢幕快照 2016-10-14 15.21.49.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.21.49.png)![螢幕快照 2016-10-14 15.22.07.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.22.07.png)

![螢幕快照 2016-10-14 15.22.38.png](https://blog.7in0.me/wp-content/uploads/2016/10/螢幕快照-2016-10-14-15.22.38.png)

![螢幕快照 2016-10-14 15.23.01.png](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.23.01.png)

現在轉到 http://your-ip-here 就可以看到“Hello world! 哈囉！”的文章。

![2016-10-14-15-24-04](https://blog.7in0.me/wp-content/uploads/2016/10/2016-10-14-15.24.04.png)

&nbsp;