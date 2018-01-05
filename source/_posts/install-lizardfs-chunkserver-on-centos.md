---
title: CentOS 安裝 Lizardfs Chunkserver 備忘錄
id: 2584
categories:
  - 應用技巧
date: 2016-11-27 21:42:53
tags:
---

這兩天對原有的 Lizardfs 的 Chunkserver 進行改造，增添新的硬碟，並且重新灌 CentOS 7.

本例中 Chunkserver 同時安裝 Shadow Master, Metalogger.

首先添加 epel repo.

```
$ sudo yum install epel-release
```

<!--more-->

然後添加 Lizardfs repo.

```
$ curl http://packages.lizardfs.com/yum/el7/lizardfs.repo | sudo tee /etc/yum.repos.d/lizardfs.repo
```

安裝 Lizardfs 的組件

```
$ sudo yum install vim lizardfs-master lizardfs-metalogger lizardfs-chunkserver
```

設定 Lizardfs

```
$ cd /etc/mfs
$ sudo rename .cfg.dist .cfg *.dist
$ sudo cp /var/lib/mfs/metadata.mfs.empty /var/lib/mfs/metadata.mfs
$ sudo vim mfsmaster.cfg
 PERSONALITY = shadow
$ sudo vim mfshdd.cfg
 /mnt/mfs # /mnt/mfs XFS filesystem for MFS
$ sudo vim /etc/hosts
 192.168.19.1 mfsmaster
$ sudo chown -R mfs:mfs /mnt/mfs
```

設定 Firewalld

```
$ sudo vim /etc/sysconfig/network-scripts/ifcfg-eno1
 ZONE="trusted"
$ sudo systemctl restart network.service
$ sudo systemctl restart firewalld.service
```

啓動 Lizardfs

```
$ sudo systemctl restart lizardfs-master
$ sudo systemctl restart lizardfs-metalogger
$ sudo systemctl restart lizardfs-chunkserver

$ sudo systemctl enable lizardfs-master
$ sudo systemctl enable lizardfs-metalogger
$ sudo systemctl enable lizardfs-chunkserver
```