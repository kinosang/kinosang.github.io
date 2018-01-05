---
title: Ubuntu 16.04 上部署 GitLab
id: 2641
categories:
  - 應用技巧
date: 2017-02-23 13:10:16
tags:
---

雖然 GitLab 推出了 one-key deb 包，但是爲了更大的靈活性，我還是選擇 step-by-step 的部署方式。

同時，我在部署過程中直接配置好了 SMTP 和 HTTPS，相關操作會註明。

官方指引：[https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md).

<!--more-->

### 準備系統

我預先添加了 50G 的存儲，手動掛載到 /home，具體操作請自行搜索。

先重新設定系統 locales

<pre>$ sudo dpkg-reconfigure locales
# Locales to be generated 中僅保留 en_US.UTF-8
# Default locale for the system environment 中選 en_US.UTF-8
$ locale # 驗證 locale</pre>

這一步可以完全按文檔來。

<pre>$ sudo apt update -y
$ sudo apt upgrade -y
$ sudo apt install -y vim
$ sudo update-alternatives --set editor /usr/bin/vim.basic

$ sudo apt install -y build-essential zlib1g-dev libyaml-dev libssl-dev libgdbm-dev libreadline-dev libncurses5-dev libffi-dev curl openssh-server checkinstall libxml2-dev libxslt-dev libcurl4-openssl-dev libicu-dev logrotate python-docutils pkg-config cmake

$ sudo adduser --disabled-login --gecos 'GitLab' git</pre>

### 安裝 Git

Ubuntu 16.04 上的 apt 安裝的 git 版本是 2.7，比 GitLab 要求的 2.8.4 低，我們要手動建置。

官方指引中使用 2.8.4，我們可以安裝最新版。

<pre>$ sudo apt remove git-core

$ sudo apt install -y libcurl4-openssl-dev libexpat1-dev gettext libz-dev libssl-dev build-essential

$ cd /tmp
$ wget https://www.kernel.org/pub/software/scm/git/git-2.11.1.tar.gz
$ tar vzxf git-2.11.1.tar.gz
$ cd git-2.11.1/
$ ./configure
$ make prefix=/usr/local all
$ sudo make prefix=/usr/local install

$ hash -r # rehash
$ git --version # 檢查 git 版本</pre>

### 安裝 Ruby

Ruby **必須是 2.3 版本**，否則後面 gem 安裝 json 時會報錯。

<pre>$ sudo apt-get remove ruby1.8

$ cd /tmp
$ wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.3.tar.gz
$ tar vzxf ruby-2.3.3.tar.gz
$ cd ruby-2.3.3
$ ./configure --disable-install-rdoc
$ make
$ sudo make install

$ hash -r

$ sudo gem install bundler --no-ri --no-rdoc</pre>

### 安裝 Go

<pre>$ sudo rm -rf /usr/local/go

$ cd /tmp
$ wget https://storage.googleapis.com/golang/go1.8.linux-amd64.tar.gz
$ tar vzxf go1.8.linux-amd64.tar.gz
$ sudo mv go /usr/local/
$ sudo ln -sf /usr/local/go/bin/{go,godoc,gofmt} /usr/local/bin/

$ hash -r
$ go version</pre>

### 安裝 Node

其中 yarn 需要安裝到 git 用戶，否則後續操作中會報錯。

<pre>$ curl --location https://deb.nodesource.com/setup_7.x | bash -
$ sudo apt-get install -y nodejs

$ wget https://yarnpkg.com/install.sh
$ sudo -u git -H bash install.sh</pre>

### 安裝並設定 PostgreSQL

<pre>$ sudo apt install -y postgresql postgresql-client libpq-dev postgresql-contrib
$ sudo -u postgres psql -d template1 -c "CREATE USER git CREATEDB;"
$ sudo -u postgres psql -d template1 -c "CREATE EXTENSION IF NOT EXISTS pg_trgm;"
$ sudo -u postgres psql -d template1 -c "CREATE DATABASE gitlabhq_production OWNER git;"
$ sudo -u git -H psql -d gitlabhq_production

gitlabhq_production&gt; SELECT true AS enabled FROM pg_available_extensions WHERE name = 'pg_trgm' AND installed_version IS NOT NULL;

# 理論上有如下信息顯示
enabled
---------
 t
(1 row)

gitlabhq_production&gt;\q

$ sudo systemctl enable postgresql</pre>

### 安裝並設定 Redis

<pre>$ sudo apt install redis-server

$ sudo cp /etc/redis/redis.conf /etc/redis/redis.conf.orig
$ sed 's/^port .*/port 0/' /etc/redis/redis.conf.orig | sudo tee /etc/redis/redis.conf
$ echo 'unixsocket /var/run/redis/redis.sock' | sudo tee -a /etc/redis/redis.conf
$ echo 'unixsocketperm 770' | sudo tee -a /etc/redis/redis.conf
$ mkdir /var/run/redis
$ chown redis:redis /var/run/redis
$ chmod 755 /var/run/redis

$ if [ -d /etc/tmpfiles.d ]; then
> echo 'd /var/run/redis 0755 redis redis 10d -' | sudo tee -a /etc/tmpfiles.d/redis.conf
> fi

$ sudo systemctl restart redis-server

$ sudo usermod -aG redis git</pre>

### 安裝 GitLab

<pre>$ cd /home/git
$ sudo -u git -H git clone https://gitlab.com/gitlab-org/gitlab-ce.git -b 8-17-stable gitlab

$ cd /home/git/gitlab

$ sudo -u git -H cp config/gitlab.yml.example config/gitlab.yml
$ sudo -u git -H editor config/gitlab.yml # 編輯 gitlab 配置
# 主要修改 gitlab -&gt; host, gitlab -&gt; email_from, git -&gt; bin_path
# HTTPS 相關 port 修改爲 443, https 修改爲 true

$ sudo -u git -H cp config/initializers/smtp_settings.rb.sample config/initializers/smtp_settings.rb
$ sudo -u git -H vim config/initializers/smtp_settings.rb
# SMTP 相關 修改 smtp 信息
$ sudo -u git -H cp config/secrets.yml.example config/secrets.yml
$ sudo -u git -H chmod 0600 config/secrets.yml

$ sudo chown -R git log/
$ sudo chown -R git tmp/
$ sudo chmod -R u+rwX,go-w log/
$ sudo chmod -R u+rwX tmp/

$ sudo chmod -R u+rwX tmp/pids/
$ sudo chmod -R u+rwX tmp/sockets/

$ sudo -u git -H mkdir public/uploads/
$ sudo chmod 0700 public/uploads

$ sudo chmod -R u+rwX builds/
$ sudo chmod -R u+rwX shared/artifacts/
$ sudo chmod -R ug+rwX shared/pages/

$ sudo -u git -H cp config/unicorn.rb.example config/unicorn.rb
$ sudo -u git -H editor config/unicorn.rb # 設定 unicorn，主要是 RAM

$ sudo -u git -H cp config/initializers/rack_attack.rb.example config/initializers/rack_attack.rb

$ sudo -u git -H git config --global core.autocrlf input
$ sudo -u git -H git config --global gc.auto 0
$ sudo -u git -H git config --global repack.writeBitmaps true

$ sudo -u git -H cp config/resque.yml.example config/resque.yml
$ sudo -u git -H editor config/resque.yml # 若需要修改 Redis socket</pre>

### 設定 GitLab 資料庫

<pre>$ sudo -u git cp config/database.yml.postgresql config/database.yml
$ sudo -u git -H chmod o-rwx config/database.yml</pre>

### 安裝 Gems

<pre>$ sudo -u git -H bundle install --deployment --without development test mysql aws kerberos</pre>

### 安裝 GitLab Shell

<pre>$ sudo -u git -H bundle exec rake gitlab:shell:install REDIS_URL=unix:/var/run/redis/redis.sock RAILS_ENV=production SKIP_STORAGE_VALIDATION=true
$ sudo -u git -H editor ../gitlab-shell/config.yml
# HTTPS 相關 檢查 gitlab_url 是否爲 HTTPS, 設置 ca_file 爲證書文件</pre>

### 安裝 gitlab-workhorse

<pre>$ sudo -u git -H bundle exec rake "gitlab:workhorse:install[/home/git/gitlab-workhorse]" RAILS_ENV=production</pre>

### 建置資料庫

<pre>$ sudo -u git -H bundle exec rake gitlab:setup RAILS_ENV=production
# Do you want to continue (yes/no)?</pre>

### 準備腳本

<pre>$ sudo cp lib/support/init.d/gitlab /etc/init.d/gitlab
$ sudo cp lib/support/init.d/gitlab.default.example /etc/default/gitlab
$ sudo update-rc.d gitlab defaults 21</pre>

### 設定 Logrotate

<pre>$ sudo cp lib/support/logrotate/gitlab /etc/logrotate.d/gitlab</pre>

### 建置素材

第一步的指令**與官方不同**，使用安裝到 git 用戶的 yarn.

<pre>$ sudo -u git -H /home/git/.yarn/bin/yarn install --production --pure-lockfile
$ sudo -u git -H bundle exec rake gitlab:assets:compile RAILS_ENV=production NODE_ENV=production</pre>

### 安裝並設定 Nginx

因爲是在全新的伺服器上安裝 GitLab，伺服器專用作 GitLab，所以我移除了 nginx 默認的 default 站點並且不對 GitLab 提供的設定檔作修改。

<pre>$ sudo apt install -y nginx

$ mkdir -p /etc/nginx/ssl/

# HTTPS 相關 這裏需要上傳證書到 /etc/nginx/ssl/
$ sudo cp lib/support/nginx/gitlab /etc/nginx/sites-available/gitlab
$ sudo ln -s /etc/nginx/sites-available/gitlab-ssl /etc/nginx/sites-enabled/gitlab-ssl
$ sudo -u git -H editor /etc/nginx/sites-enabled/gitlab-ssl
# HTTPS 相關 修改 YOUR_SERVER_FQDN, ssl_certificate, ssl_certificate_key
$ sudo rm /etc/nginx/sites-enabled/default # 移除默認站點

$ sudo nginx -t

$ sudo systemctl restart nginx
$ sudo systemctl enable nginx</pre>

### 檢查應用狀態

<pre>$ sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production</pre>

### 啓動 GitLab

<pre>$ sudo systemctl start gitlab</pre>

### 收尾工作

檢查 GitLab 狀態

<pre>$ sudo -u git -H bundle exec rake gitlab:env:info RAILS_ENV=production</pre>

設定 GitLab

參訪伺服器 IP 或網域進行設定。