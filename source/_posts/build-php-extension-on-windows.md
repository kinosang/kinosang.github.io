---
title: 在 Windows 上編譯 PHP 擴充套件
date: 2020-03-29 14:44:29
categories:
  - 軟體開發
tags:
---

前幾天需要在 Windows 上安裝 php-zmq 擴充套件, 奈何尋遍網路找不到為 PHP 7.4 編譯好的 DLL, 只好自行編譯.

編譯過程參考了 *Build your own PHP on Windows*[^1] 和 *How to build PHP Extension on Windows*[^2].

<!--more-->

### 準備工作

1. 首先需要安裝 Visual C++ Build Tools 2017.

從 `https://aka.ms/vs/15/release/vs_buildtools.exe` 下載安裝檔案.

執行下面的指令進行安裝.

```powershell
> .\vs_buildtools.exe --quiet --wait --norestart --nocache --installPath C:\BuildTools --add  Microsoft.VisualStudio.Workload.VCTools
```

*上述指令系自動安裝, 不會顯示任何視窗, 如需瞭解安裝進度可將 --quiet 刪掉*

2. 下載 [PHP-SDK](https://github.com/Microsoft/php-sdk-binary-tools) 並解壓縮, 目錄結構如下.

```
php-sdk
 |-bin
 |-lib
 |-msys2
 |-pgo
 ...
```

3. 準備 SDK

```powershell
> .\phpsdk-starter.bat -c vc15 -a x64
$ phpsdk_buildtree php-dev
```

4. 下載 [PHP 程式碼](https://windows.php.net/download/) 並解壓縮, 目錄結構如下.

```
php-sdk
 |-vc15
    |-x64
      |-php-7.4.4-src
```

5. 準備程式庫

```bash
$ cd vc15\x64\php-7.4.4-src
$ phpsdk_deps -u
...
Updates performed successfully.
```

6. 編譯 PHP

```bash
$ buildconf
$ configure --disable-all --enable-cli # ZTS, 若需 NTS 使用下面一行
$ configure --disable-all --enable-cli --disable-zts # NTS, 若需 ZTS 使用上面一行
...
Type ‘nmake’ to build PHP
$ nmake
```

### 編譯擴充套件

1. 下載 [PHP-ZMQ 程式碼](https://github.com/zeromq/php-zmq) 並解壓縮, 目錄結構如下.

```
php-sdk
 |-vc15
    |-x64
      |-php-7.4.4-src
        |-ext
          |-zmq
            |-config.m4
            |-config.w32
            ...
```

2. 下載 [ZeroMQ 程式碼](https://zeromq.org/download/) 和 [ZeroMQ Visual Studio 15 2017 x64](https://zeromq.org/download/) 並解壓縮, 目錄結構如下.

```
php-sdk
 |-vc15
    |-x64
      |-deps
        |-bin
          |-libzmq-v141-mt-4_3_2.dll
        |-include
          |-zmq.h
        |-lib
          |-libzmq.lib <- libzmq-v141-mt-4_3_2.lib
```

3. 編譯 PHP-ZMQ

```bash
$ nmake clean
$ buildconf --force # 重建 configure, 新增 --with-zmq
$ configure --disable-all --enable-cli --with-zmq=shared # ZTS, 若需 NTS 使用下面一行
$ configure --disable-all --enable-cli --with-zmq=shared --disable-zts # NTS, 若需 ZTS 使用上面一行
...
Type ‘nmake’ to build PHP
$ nmake
```

編譯成功的 `php_zmq.dll` 在:

```bash
php-sdk\php-dev\vc15\x64\php-7.4.4-src\x64\Release_TS\php_zmq.dll # ZTS
php-sdk\php-dev\vc15\x64\php-7.4.4-src\x64\Release\php_zmq.dll #NTS
```

*編譯成果在 [zeromq/php-zmq#193](https://github.com/zeromq/php-zmq/issues/193#issuecomment-605456811)*

### 使用擴充套件

將 `php_zmq.dll` 放到 `php/ext`, 將 `libzmq-v141-mt-4_3_2.dll` 放到 `php`, 然後在 `php.ini` 新增:

```ini
extension=zmq
```

[^1]: https://wiki.php.net/internals/windows/stepbystepbuild_sdk_2
[^2]: https://medium.com/@erinus/how-to-build-php-extension-on-windows-d1667290f809
