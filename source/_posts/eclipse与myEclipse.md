---
title: eclipse与myEclipse
date: 2017-03-01 21:18:07
tags: JAVA
categories: 工具
---
### 一、eclipse添加了tomcat启动成功但404

> 需要修改一下，具体如下：
<!--more-->
1. remove server里面的所有项目
2. clean一下server
3. 双击server，出现了server的配置项
4. ServerLocations选择第二个：Use Tomcat installation(takes control of Tomcat installation)
5. deploy path改成webapps
6. ServerPath改成安装的tomcat的安装目

### 二、myeclipse设置代理
`window-->preferences-->general-->network connections 选中 manual proxy configuration`
由于我安装了goagent，所以我设置http和https代理全为127.0.0.1:8087

这样我安装软件只要在：

`help->install from Catalog`就OK了

### 三、myeclipse默认不把src下面的java文件编译到classes中

需要确保project->build automatically 已经被选上.
可以参考[这篇文章](http://blog.csdn.net/coolwubo/article/details/9612545)