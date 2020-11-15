---
title: GitLab 8.17 中文化
categories:
  - 雜物箱
date: 2017-03-10 16:53:12
tags:
---

之前, 我安裝了 GitLab 8.17\. 現在有需求將其中文化.

本文參考了 [https://larryli.cn/2015/07/644905](https://larryli.cn/2015/07/644905).

<!--more-->

### 簡單粗暴

```bash
$ sudo systemctl stop gitlab

$ cd /tmp
$ sudo -u git -H git clone https://gitlab.com/xhang/gitlab.git -b 8-17-stable-zh gitlab-zh

$ cd /home/git/gitlab
$ sudo -u git -H cp -R /tmp/gitlab-zh/* .

$ sudo systemctl start gitlab
```

### 規範做法

在安裝 GitLab 的時候, 直接使用前文中的中文版進行安裝.

或安裝完成後, 備份 /home/git/gitlab, clone 中文版到 /home/git/gitlab, 然後複製備份中的文件到 /home/git/gitlab.
