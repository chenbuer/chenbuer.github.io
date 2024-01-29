---
title: 给github增加本地代理proxy
date: 2024-1-29 19:08:00
categories: GIT
tags: 工具
---

## 一、概述
GitHub被block，而且proxy server是基于https的，所以直接设置ssh代理有点麻烦。所以，将ssh改成https。
1. 购买VPS，设置https的代理服务
2. 设置本地的转发服务
3. 设置git proxy，指向本地proxy 服务
4. 修改本地已存在的repo，由ssh改成https
<!--more-->

## 二、设置过程

### 2.1 购买VPS，设置https代理服务
可以参考[这个教程](https://github.com/chenbuer/haoel.github.io/blob/master/gost_install.ipynb)

### 2.2 设置本地的转发服务
没有比较好的local https代理客户端，所以用gost监听一个端口，将git指向该端口。

```shell
gost.exe -L :8888 -F https://www.proxyServer.xyz:443?auth=base64(user:passwod)
```
将用户名密码（user:password）进行base64编码后替换

### 2.3 设置git proxy，指向本地proxy 服务
```shell
git config --global https.proxy http://127.0.0.1:8888

git config --global https.proxy https://127.0.0.1:8888

git config --global --unset http.proxy

git config --global --unset https.proxy
```
修改之后用`git config --list`命令查看。

### 2.4 修改本地已存在的repo，由ssh改成https
打开本地仓库的.git文件夹，修改config文件，e.g 原来的ssh配置为
```shell
[remote "origin"]
	url = git@github.com:chenbuer/chenbuer.github.io.git
	fetch = +refs/heads/*:refs/remotes/origin/*
```
直接替换`url`，由ssh格式改成https格式
```shell
url = https://github.com/chenbuer/chenbuer.github.io.git
```
即可。

至此，就可以像利用ssh一样利用https去操作repo了。


【完毕】