---
title: 轉投 MariaDB 的懷抱！
id: 1517
categories:
  - 互聯網絡
  - 應用技巧
date: 2014-01-08 20:51:05
tags:
---

CentOS 安裝 Nginx + PHP + MariaDB | MySQL自動最佳化

眾所周知，萬惡的 Orcal 正在逐步封閉 MySQL 的原始碼，這讓很多人十分不爽。比如維基百科、維基百科還有維基百科，哦，Google 也正在嘗試從 MySQL 遷移到 MariaDB。

[caption id="attachment_1523" align="alignnone" width="300"][![MariaDB Foundation](/wp-content/uploads/2014/01/ice_logo-5dcea9e47b780ff52f75c3c3304d54827f56211e-300x300.png)](/wp-content/uploads/2014/01/ice_logo-5dcea9e47b780ff52f75c3c3304d54827f56211e.png) MariaDB Foundation's Logo[/caption]

技術宅社區的技術之一，氺菓  剛剛把 gn00 轉換到 MariaDB。於是，7IN0 也跟風了一把，嘗試進行轉換。

這個過程從 GMT+08:00 2014/01/08 19:10 開始。

<!--more-->

首先，保險起見，我創建了一個快照。

[caption id="attachment_1518" align="alignnone" width="300"][![Snapshot(DigitalOcean)](/wp-content/uploads/2014/01/H@_5NOHHWXCFOL8GYU-300x151.jpg)](/wp-content/uploads/2014/01/H@_5NOHHWXCFOL8GYU.jpg) Snapshot(DigitalOcean)[/caption]

然後，經過一番考慮，我直接用 CentOS 6.5 x86 重灌了 Droplet（VPS）。這樣做的原因有三：

1.  之前不小心使用 yum update 更新了系統 kernel，導致每次 Boot 有一定几率不能正常啟動
2.  之前圖省事使用了 lnmp 套件，這個套件是通過 make 、 make install 的方式灌裝組件的，並且具備一個統一管理 N/M/P 的工具，修改起來十分不方便
3.  之前幾次折騰導致系統中殘留了大量垃圾

&nbsp;

然後使用之前創建的 Snapshot 新建一個 Droplet（命名為 bk，原有 Droplet 命名為 main），兩個 Droplet 都開啟Private Network，方便稍後傳輸文件。

## 一、安裝 MariaDB：

參閱[Install MariaDB Database in RHEL/CentOS/Fedora and Debian/Ubuntu](http://www.tecmint.com/install-mariadb-in-linux/ "Install MariaDB Database in RHEL/CentOS/Fedora and Debian/Ubuntu")

在 main 上執行：

### 添加源

<pre class="prettyprint linenums">$ vim /etc/yum.repos.d/MariaDB.repo
# MariaDB 5.5 RedHat repository list - created 2013-08-11 14:29 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/5.5/rhel6-x86
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1</pre>

### 安裝並啟用 MariaDB

<pre class="prettyprint linenums">$ yum -y install MariaDB-server MariaDB-client MariaDB-common MariaDB-compat MariaDB-shared
...
$ service mysql start
...
$ chkconfig --levels 235 mysql on</pre>

### 初次設定 root 帳戶

<pre class="prettyprint linenums">$ mysql_secure_installation
/wp-content/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):

OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] Y

New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!</pre>

## 二、安裝 Nginx 和 PHP

安裝 PHP 需要使用 remi 源

### 添加源

參見[Installing RHEL EPEL Repo on Centos 5.x or 6.x](http://www.rackspace.com/knowledge_center/article/installing-rhel-epel-repo-on-centos-5x-or-6x)

<pre class="prettyprint linenums">$ wget http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
$ wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
$ rpm -Uvh remi-release-6*.rpm epel-release-6*.rpm</pre>

記得將 /etc/yum.repos.d/remi.repo 中的 [remi] 設定為 enabled=1
還有 Nginx 的源

<pre class="prettyprint linenums">$ vim /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/6/$basearch/
gpgcheck=0
enabled=1</pre>

### 安裝並啟用 Nginx

<pre class="prettyprint linenums">$ yum install nginx
$ service nginx start
$ chkconfig --levels 235 nginx on</pre>

### 安裝並啟用 PHP-FPM

<pre class="prettyprint linenums">$ yum install php-fpm php-mysqlnd php-gd php-curl php-mcrypt php-apc
$ service php-fpm start
$ chkconfig --levels 235 php-fpm on</pre>

如何設定 Nginx + PHP-FPM 此處不在敘述。

## 三、傳輸文件

### sftp 傳輸檔案夾

<pre class="prettyprint linenums">$ scp -r user@ip:/path/to/dir /local/dir</pre>

### MySQL 數據匯入 MariaDB

我直接使用 PHPMyAdmin 匯出 MySQL 數據庫為 SQL 文檔，然後匯入 MariaDB 中。

&nbsp;

# 一點小動作

讓系統自動優化數據庫，以提高效率

<pre class="prettyprint linenums">$ crontab -e
0 0 * * 0 /wp-content/bin/mysqlcheck --all-databases --optimize --auto-repair -u root -pYourRootPassword &gt; /dev/null 2&gt;&amp;1</pre>

當前時間 GMT+08:00 2014/01/08 20:00 ，全部工作完成，移除 bk。