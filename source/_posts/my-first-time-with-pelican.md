---
title: Pelican 初體驗
id: 1699
categories:
  - 應用技巧
date: 2014-01-21 10:52:49
tags:
---

2014年1月18日, 在下將 7IN0's Arts 換為使用 Pelican 驅動.

[caption id="attachment_1719" align="alignnone" width="300"][![Pelican](/wp-content/uploads/2014/01/pelican-300x121.png)](/wp-content/uploads/2014/01/pelican.png) Pelican[/caption]

Pelican 是一個靜態網站生成器 (Static Site Generator) , 基於 Python, 提供豐富的設定, 主題和插件, 可以使用 PIP 進行安裝.

CentOS 的 Python 是 2.6 版本, Pelican 則需求 2.7+, 因此, 先編譯安裝 Python 2.7

<!--more-->

```bash
$ wget -c http://python.org/ftp/python/2.7.6/Python-2.7.6.tgz
...
$ tar vzxf Python-2.7.6.tgz
...
$ cd Python-2.7.6
$ ./configure
...
$ make && make install
...
$ python --version
Python 2.6.6
$ mv /wp-content/bin/python /wp-content/bin/python.bak
$ ln -s /wp-content/local/bin/python2.7 /wp-content/bin/python
$ python --version
Python 2.7.6
```

有人說 yum 不兼容 Python 2.7, 需要修改下

```bash
$ vim /wp-content/bin/yum
```

把第一行的

```bash
#!/wp-content/bin/python
```

改成

```bash
#!/wp-content/bin/python2.6
```

然後安裝 setuptools

```bash
$ wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py -O - | python
```

和 pip

```bash
$ wget https://raw.github.com/pypa/pip/master/contrib/get-pip.py -O - | python
```

使用 pip 安裝 pelican

```bash
$ pip install pelican
```

在下使用 Markdown 進行創作, 因此需要額外安裝 Markdown 支援

[caption id="attachment_1720" align="alignnone" width="300"][![Markdown](/wp-content/uploads/2014/01/MarkdownDocumentIcon-300x300.png)](/wp-content/uploads/2014/01/MarkdownDocumentIcon-e1390272755111.png) Markdown[/caption]

```bash
$ pip install Markdown
```

測試一下

```bash
$ pelican --help
usage: pelican [-h] [-t THEME] [-o OUTPUT] [-s SETTINGS] [-d] [-v] [-q] [-D]
               [--version] [-r]
               [path]

A tool to generate a static blog, with restructured text input files.

positional arguments:
  path                  Path where to find the content files. (default: None)

optional arguments:
  -h, --help            show this help message and exit
  -t THEME, --theme-path THEME
                        Path where to find the theme templates. If not
                        specified, itwill use the default one included with
                        pelican. (default: None)
  -o OUTPUT, --output OUTPUT
                        Where to output the generated files. If not specified,
                        a directory will be created, named "output" in the
                        current path. (default: None)
  -s SETTINGS, --settings SETTINGS
                        The settings of the application, this is automatically
                        set to pelicanconf.py if a file exists with this name.
                        (default: None)
  -d, --delete-output-directory
                        Delete the output directory. (default: None)
  -v, --verbose         Show all messages. (default: None)
  -q, --quiet           Show only critical errors. (default: None)
  -D, --debug           Show all message, including debug messages. (default:
                        None)
  --version             Print the pelican version and exit.
  -r, --autoreload      Relaunch pelican each time a modification occurs on
                        the content files. (default: False)
```

現在, 新建一個檔案夾並在其中開始一個 Pelican 工程

```bash
$ mkdir test
$ cd test
$ pelican-quickstart
...
```

工程的檔案結構是這樣的：

```
test/
├── content
│   └── (pages)
├── output
├── develop_server.sh
├── fabfile.py
├── Makefile
├── pelicanconf.py
└── publishconf.py
```

通過修改 pelicanconf.py 設定站點, 修改 Makefile 可以改變目標檔案夾 (默認是 ./output)

把寫好的 md 檔案丟進 content 裏, 然後

```bash
$ make html
```

GJ, 工作就這樣完成了!
