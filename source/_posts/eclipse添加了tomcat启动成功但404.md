---
title: eclipse添加了tomcat启动成功但404
date: 2017-03-01 21:18:07
tags: [eclipse,tomcat]
categories: JAVA
---

需要修改一下，具体如下：
<!--more-->
1. remove server里面的所有项目
2. clean一下server
3. 双击server，出现了server的配置项
4. ServerLocations选择第二个：Use Tomcat installation(takes control of Tomcat installation)
5. deploy path改成webapps
6. ServerPath改成安装的tomcat的安装目