---
title: Deis on DigitalOcean 部署小記
id: 1941
categories:
  - 應用技巧
date: 2014-11-19 14:18:00
tags:
---

![Deis](/wp-content/uploads/2014/11/menu-logo.png)

Deis 提供的 Documents 比較零散，很多人看得雲裡霧裡。

在下參悟多日，經過數次嘗試（S/L 大法好），終於成功在 DigitalOcean 成功部署 Deis PaaS 系統（3 nodes）。

<!--more-->

### 前期準備

首先你需要 clone dies 提供的一個 git 倉庫，裡面提供了一些工具用來進行前期準備。

```
$ git clone https://github.com/deis/deis.git
```

然後，最好為 Deis 專門生成一個 RSA Key

```
$ ssh-keygen -q -t rsa -f ~/.ssh/deis -N '' -C deis
```

接著在 git 倉庫根目錄執行下面的指令生成一個最小集群（3 nodes, 2G RAM）

```
$ make discovery-url
$ gem install docl
$ docl authorize #在這裡你需要前往 DigitalOcean 的 Apps&API 頁面獲取一個 Personal Access Tokens
$ docl upload_key deis ~/.ssh/deis.pub
$ docl keys
deis (id: 12345)
$ docl regions --metadata --private-networking
Amsterdam 2 (ams2)
Amsterdam 3 (ams3)
London 1 (lon1)
New York 3 (nyc3)
Singapore 1 (sgp1)
$ ./contrib/digitalocean/provision-do-cluster.sh nyc3 12345 2GB
```

設定 DNS，你需要設定如下記錄（用上一條指令返回的三個 IP 取代下面對應的 IP）：

```
deis-1   IN A    104.131.93.162
deis-2   IN A    104.131.47.125
deis-3   IN A    104.131.113.138
*   CNAME   @
@   IN A    104.131.93.162
@   IN A    104.131.47.125
@   IN A    104.131.113.138
```

### 設定 Deis

因為 DigitalOcean 沒有提供安全組設定，所以使用 iptables 來實現轉發，在 git 倉庫根目錄使用下面的命令（記得用你自己的域名替換 example.com）

```
$ for i in 1 2 3; do ssh core@deis-$i.example.com 'bash -s' < contrib/util/custom-firewall.sh; done
```

在你的電腦中安裝 deisctl（[參考](http://docs.deis.io/en/latest/installing_deis/install-deisctl/#install-deisctl)）
然後確認你所安裝的 deisctl 版本和你的 deis node 一致

```
$ deisctl --version
1.0.1
```

啟動 ssh-agent 並且導入 RSA 密匙

```
$ eval `ssh-agent -s`
$ ssh-add ~/.ssh/deis
```

準備環境變量，替換下面的 IP 為三個 node 中任意一個的 IP 或者域名

```
$ export DEISCTL_TUNNEL=104.131.93.162
```

設定私鈅和節點域名，安裝并啟動 deis 平台（這一步之前記得到 DigitalOcean 裡面創建個快照）

```
$ deisctl config platform set sshPrivateKey=~/.ssh/deis
$ deisctl config platform set domain=example.com
$ deisctl install platform
$ deisctl start platform
```

這樣部署工作就全部完成了

注：如果執行 deisctl install platform 時出現 timeout，可以執行 export FLEET_ETCD_REQUEST_TIMEOUT=5000 增大超時時間設定。
