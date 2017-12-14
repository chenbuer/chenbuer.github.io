---
title: MAC操作
date: 2017-12-15 1:20:20
categories: 工具
tags: 工具
---
一. 给mac安装Consolas 字体

```bash
# brew is a part of Mac OS X package manager called Homebrew (http://brew.sh/).
brew install cabextract
cd ~/Downloads
mkdir consolas
cd consolas
curl -O http://download.microsoft.com/download/f/5/a/f5a3df76-d856-4a61-a6bd-722f52a5be26/PowerPointViewer.exe
cabextract PowerPointViewer.exe
cabextract ppviewer.cab
open CONSOLA*.TTF
# Press Intall font. That is all.
```
<!--more-->
[参考链接](http://ikato.com/blog/how-to-install-consolas-font-on-mac-os-x.html)

二. 安装的软件

[IDEA](http://www.cnblogs.com/wang1024/p/7485758.html)

[office](http://www.jianshu.com/p/2172835cfb17)

[eclipse](http://www.eclipse.org/downloads/)

[docker](https://www.docker.com/docker-mac)

[docker-mysql](https://hub.alauda.cn/repos/alauda/mysql)
