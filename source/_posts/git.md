---
title: GIT
date: 2017-12-11 20:29:20
categories: GIT
tags: 工具
---
### 一. 设置代理
```bash
git config --global http.proxy http://127.0.0.1:8087
git config --global https.proxy http://127.0.0.1:8087
git config --global http.sslverify false
```
<!--more-->
也可以设置socket代理，不错：
```bash
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

### 二. 撤销代理
```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 三. 如果要查当前的代理配置
```bash
git config --global --get http.proxy
git config --global --get https.proxy
```

### 四. 切换到远程分支
```bash
git checkout -t origin/hexo
```

### 五. Windows下手动改修配置文件
在Windows下找不到的gitconfig文件，但是我们都知道的Linux下`~/.gitconfig`。所以，用gitbash吧：
```bash
$ vi ~/.gitconfig
```

### 六. 用https也不用每次都输入密码
很多环境下并不能用ssh，所以`git clone`的时候Clone with SSH也不好使，只能用https协议。用https协议在每次提交的时候，都要手动输入用户名和密码。所以修改本地repository下的git配置文件。
修改前：
```xml
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
        hideDotFiles = dotGitOnly
[remote "origin"]
        url = https://github.com/chenbuer/chenbuer.github.io.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
[branch "hexo"]
        remote = origin
        merge = refs/heads/hexo
```
修改之后：
![modGitCfg](/images/git/modGitCfg.png)

### 七、本地添加github远程
首先需要在github上添加repo为test，然后在本地项目下执行命令：
```bash
git init
git remote add origin https://github.com/chenbuer/test //关联远程仓库
```