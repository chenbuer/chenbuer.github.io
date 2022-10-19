---
title: VPN操作
date: 2018-2-9 18:20:20
categories: MAC
tags: 工具
---
### 一. 客户端选择
Windows可以选择OpenVPN客户端

mac可以选择[tunnelblick](https://tunnelblick.net/downloads.html)

<!--more-->

### 二. 登录之后可能不能自动修改DNS地址

那就需要手动设置。
在的“网络偏好设置”里面设置总是很麻烦。用命令行设置，[参考链接](http://blog.51cto.com/liongmagezi/1386792)

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

### 三、其他客户端
- ssr：
    - mac:[ShadowsocksX-NG-R](https://github.com/wzdnzd/ShadowsocksX-NG-R)
    - windows:[shadowsocksr-csharp](https://github.com/shadowsocksrr/shadowsocksr-csharp),[ShadowsocksR-Windows](https://github.com/HMBSbige/ShadowsocksR-Windows)
- ss：[ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG)
