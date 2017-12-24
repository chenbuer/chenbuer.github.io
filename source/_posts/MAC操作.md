---
title: MAC操作
date: 2017-12-15 1:20:20
categories: MAC
tags: 工具
---
### 一. 给mac安装Consolas 字体

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

### 二. 安装的软件

[office](http://www.jianshu.com/p/2172835cfb17)

[docker](https://www.docker.com/docker-mac)

[docker-mysql](https://hub.alauda.cn/repos/alauda/mysql)

[snipaste](https://zh.snipaste.com/)

[安装Java8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)-[卸载Java9](https://gist.github.com/schnell18/bcb9833f725be22f6acd01f94b486392)

[zookeeper](https://zookeeper.apache.org/releases.html#download)-[安装参考](http://www.jianshu.com/p/0ba61bf7149f)

[IDEA](http://www.cnblogs.com/wang1024/p/7485758.html)

[eclipse](http://www.eclipse.org/downloads/)

[开启IPV6](https://github.com/XX-net/XX-Net/wiki/IPv6-Mac)

[快捷设置窗口大小Spectacle](https://www.zhihu.com/question/21075430)

### 三、关闭仪表盘(dashboard)

系统偏好设置->调度中心（或者直接搜索dashboard）->仪表盘的下拉框选择关闭

[参考链接](https://www.maczd.com/post/39.html)

### 四、Mac常用键的简写

![Mac常用键的简写](https://github.com/chenbuer/markdownImgs/blob/master/blog/mac%E5%B8%B8%E7%94%A8%E9%94%AE.png?raw=true)