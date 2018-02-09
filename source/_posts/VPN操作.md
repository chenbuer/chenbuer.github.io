---
title: VPN操作
date: 2018-2-9 18:20:20
categories: MAC
tags: 工具
---
### 一. mac的客户端选择tunnelblick
[客户端下载地址](https://tunnelblick.net/downloads.html)


### 二. 登录之后可能不能自动修改DNS地址

那就需要手动设置。
在的“网络偏好设置”里面设置总是很麻烦。用命令行设置，[参考链接](http://blog.51cto.com/liongmagezi/1386792)

<!--more-->
```bash
➜ /Users/buer $ networksetup -listallnetworkservices
An asterisk (*) denotes that a network service is disabled.
Wi-Fi
iPhone USB
Thunderbolt Bridge
Bluetooth PAN
➜ /Users/buer $ sudo networksetup -setdnsservers Wi-Fi 192.168.121.20
```
操作完了记得改回来
```bash
sudo networksetup -setdnsservers Wi-Fi 8.8.8.8
```
