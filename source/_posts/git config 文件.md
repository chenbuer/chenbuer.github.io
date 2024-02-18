---
title: git config 文件
date: 2024-1-29 19:35:00
categories: GIT
tags: 工具
---

## 一、概述
该文件备份本地git 配置信息
<!--more-->

## 二、设置过程
```shell
[user]
	email = xx@xx.com
	name = xx
[color]
	    ui = true
[alias]
	    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
	    changes = diff --name-status -r
	    diffstat = diff --stat -r
            whatis = show -s --pretty='tformat:%h (%s, %ad)' --date=short
	    pwhatis = show -s --pretty='tformat:%h, %s, %ad' --date=short
            ci = commit
	    co = checkout
            st = status
	    br = branch
            df = diff
	    cp = cherry-pick
[push]
	    default = simple
[core]
	    editor = vim
[merge]
	    tool = vimdiff
[diff]
	    tool = vimdiff
[mergetool]
	    prompt = false
[difftool]
	    prompt = false
[filter "lfs"]
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f
	process = git-lfs filter-process
	required = true
[https]
	proxy = http://127.0.0.1:6666
[http]
	proxy = http://127.0.0.1:6666
```


【完毕】