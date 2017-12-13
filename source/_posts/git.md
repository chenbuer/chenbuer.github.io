---
title: GIT
date: 2017-12-11 20:29:20
categories: GIT
tags: 工具
---
一. 设置代理
```bash
git config --global http.proxy http://proxy.chenbuer.com:8080
git config --global https.proxy http://proxy.chenbuer.com:8080
git config --global http.sslverify false
```
<!--more-->

二. 撤销代理
```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

三. 如果要查当前的代理配置
```bash
git config --global --get http.proxy
git config --global --get https.proxy
```

四. 切换到远程分支
```bash
git checkout -t origin/hexo
```