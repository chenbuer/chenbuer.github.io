---
title: maven使用
date: 2017-02-16 23:26:25
categories: java
tags: maven
---

1. maven配置(本地仓库配置和本地设置、以及在eclipse中的配置)

下载完成之后，创建环境变量`M2_HOME=E:\apache-maven-3.3.9`也就是指向安装目录。在path环境变量中添加`%M2_HOME%\bin`也就是指向安装目录。在path环境变量中添加
在eclipse中Window->Preference->Maven4MyEclipse中设置Installationsd到达安装目录，User Settins中设置配置文件在`E:\maven-repo\settings.xml`


2. 错误`-Dmaven.multiModuleProjectDirectory maven myeclipse`

或者说是错误`Check $M2_HOME environment variable and mvn script match. `，解决方法是：
```
可以设一个环境变量M2_HOME指向你的maven安装目录
M2_HOME=D:\Apps\apache-maven-3.3.1
然后在Window->Preference->Java->Installed JREs->Edit
在Default VM arguments中设置
-Dmaven.multiModuleProjectDirectory=$M2_HOME
```
具体可以[参考路径](http://fxb4632242.iteye.com/blog/2193945)
在这个路径下还有的关于[创建maven工程](http://panyongzheng.iteye.com/blog/2200514)的方法

3. [修改maven中央仓库的方法](http://blog.csdn.net/whh743/article/details/53579668)