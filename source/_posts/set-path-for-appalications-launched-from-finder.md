---
title: 為從 Finder 啟動的應用程式設定 PATH 環境變數
date: 2018-05-19 14:05:38
categories:
  - 應用技巧
tags:
---

今天在 V2EX 看到有網友詢問 `telnet://` protocol 無法正常工作的問題，稍微做了一些調查。

<!--more-->

![Set URL Schemes handler](87c01ec7gy1frgld5khmuj21f00vidlg.jpg)

先將 `telnet://` 啟動的默認應用程式改為 iTerm2，然後使用指令 `open telnet://111`。

![Failed to exec telnet](87c01ec7gy1frglghmae4j20vk04cjs4.jpg)

可以看到提示找不不到 `telnet` 可執行檔，PATH 環境變數只包含少數幾個路徑。

![System PATHs](87c01ec7gy1frgmkbr74tj207s06e74m.jpg)

而經檢查，確認我已經在 `/etc/paths` 中設定的一系列路徑。

通過 Google 檢索，了解到自 Mac OS X 10.9 開始，`/etc/paths`, `/etc/launchd.conf` 和 `~/.MacOSX/environment.plist` 不再對 Finder 啟動的應用程式有效，解決辦法有二。

### 辦法一 修改 App 的 `Info.plist` 設定 `LSEnvironment`

![Set PATH in Info.plist](87c01ec7gy1frgmxq0vwcj218k03amyf.jpg)

用 Xcode 修改 `/Applications/iTerm.app/Contents/Info.plist`，注意 `LSEnvironment` 被自動識別為 `Environment variables`。

```xml
<key>LSEnvironment</key>
<dict>
     <key>PATH</key>
     <string>/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin:</string>
</dict>
```

然後執行下面的指令更新 LaunchServices 資料庫

```bash
$ /System/Library/Frameworks/CoreServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -v -f /Applications/iTerm.app
lsregister: processing /Applications/iTerm.app
lsregister: registered /Applications/iTerm.app
```

### 辦法二 使用 `launchctl` 設定 PATH

*本辦法只在 Mac OS X 10.10 及之後版本有效*

```bash
$ sudo launchctl config user path /usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin
Configuration applied. You must reboot for changes to take effect.
```

然後重新開機。