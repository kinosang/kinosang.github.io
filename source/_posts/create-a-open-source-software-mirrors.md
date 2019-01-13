---
title: 校內開源軟體鏡像站架設記
id: 1922
categories:
  - 互聯網絡
date: 2014-09-20 22:50:00
tags:
---

有人強烈要求我更新網誌，這兩天又正好在折騰這個，發出來求批評求指正。

先說一下所選擇的伺服器的情況：

Dell PowerEdge R210

CPU  Intel Xeon X3430 2.4GHz

Mem 6 GB

Ethernet Broadcom BCM5716 Gigabit Ethernet

HDD sda 6TB（SAS Raid, /media/RAID）sdb 1TB（6GB swap, 200M /boot, 剩餘 /） sdc 2TB（/data, 其他用途）

<!--more-->

因為是實驗性質，暫時還沒有使用虛擬化等技術來保護鏡像站。

首先確定將要對下面的源進行鏡像：

> apache, archlinux, centos, epel, fedora, linux-kernel, mariadb, opensuse, ubuntu

貼上核心程式（mirrors_sync）

```
#!/bin/sh

. /etc/init.d/functions

statusfile=/media/RAID/mirrors/.status/$1.txt

  status=`cat $statusfile`
  [ "$status" = "synchronizing" ] && exit 0

  case $1 in
    apache)
      upstream=rsync://rsync.apache.org/apache-dist/
      ;;
    archlinux)
      upstream=rsync://mirrors.hustunique.com/archlinux/
      ;;
    centos)
      upstream=rsync://mirrors.ustc.edu.cn/centos/
      ;;
    epel)
      upstream=rsync://dl.fedoraproject.org/fedora-epel/
      ;;
    fedora)
      upstream=rsync://dl.fedoraproject.org/fedora-enchilada/linux/
      ;;
    linux-kernel)
      upstream=rsync://rsync.kernel.org/pub/
      ;;
    mariadb)
      upstream=rsync.osuosl.org::mariadb
      ;;
    opensuse)
      upstream=rsync://mirrors.hustunique.com/opensuse/
      ;;
    ubuntu)
      upstream=rsync://archive.ubuntu.com/ubuntu/
      ;;
    *)
      echo "No specific upstream ( $1 ) found."
      exit 2
      ;;
  esac

  echo "synchronizing" > $statusfile

  options=" rsync -vaH --timeout=180 --numeric-ids --delete --delete-after --delay-updates --log-file=/media/RAID/mirrors/.status/rsync.log --log-file-format='%i %o %f %M %t' --exclude-from=/media/RAID/mirrors/.config/$1 $upstream /media/RAID/mirrors/$1/"
  daemon nohup $options

  RETVAL=$?
  if [ $RETVAL -eq 0 ]; then
    echo "completed" > $statusfile
  else
    echo "failed" > $statusfile
  fi
  echo
  sleep 10s
  return $RETVAL
```

然後創建一個 shell 文件（uuMirrors）

```
#!/bin/sh
nohup /root/mirrors_sync apache &
nohup /root/mirrors_sync archlinux &
nohup /root/mirrors_sync centos &
nohup /root/mirrors_sync epel &
nohup /root/mirrors_sync fedora &
nohup /root/mirrors_sync linux-kernel &
nohup /root/mirrors_sync mariadb &
nohup /root/mirrors_sync opensuse &
nohup /root/mirrors_sync ubuntu &
```

最後，添加一條 cron，每 30 分鐘執行一次

```
*/30 * * * * /root/uuMirrors >> /dev/null 2>&1
```

這樣，上面提到的源就會同步到 /media/RAID/mirrors，狀態保存到 /media/RAID/mirrors/.status 中。

下面美化 web indexlist，在下選擇 h5ai 進行修改后使用。

（圖中還有 nginx 源，是使用 wget 進行 clone 的，方案十分不成熟，暫時不公開了）

[![uuMirrors](/wp-content/uploads/2014/09/uuMirrors-300x187.png)](/wp-content/uploads/2014/09/uuMirrors-e1411224684522.png)