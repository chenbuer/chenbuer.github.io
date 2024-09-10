---
title: cloudflare tunnel
date: 2024-9-10 22:30:52
categories: 运维
tags: 网络
---

cloudflare tunnel是内网穿透工具，相比zerotier和tailscale要快很多，满足日常可用。
## 一、前置条件
需要一个域名托管在cloudflare
<!--more-->
## 二、使用步骤
### 2.1 进入配置界面
[cloudflare tunnel](https://www.cloudflare.com/zh-cn/products/tunnel/)是`cloudflare zero trust`的一个产品，登录dashboard -> Zero Trust -> Networks -> Tunnels -> create a tunnel.
### 2.2 创建tunnel
输入一个tunnel name之后，选择一个环境，我是使用的一个Raspberry Pi上安装的ubuntu server24.04，所以选择os为Debian、architecture为arm64-bit。
### 2.3 服务器设置
```shell
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb && 
sudo dpkg -i cloudflared.deb && 
sudo cloudflared service install < token >
```
服务器端设置完成之后，到tunnel的dashboard上看，status为HEALTHY。

服务器运行了`sudo cloudflared service install `之后就会创建`/etc/systemd/system/cloudflared.service`服务，会自启动。
### 2.4 配置cloudflare的tunnel
edit tunnel页面上除了overall基本配置之外，还有Public Hostname和private Network。

Public Hostname就是将内网的服务映射出去，类似于frp。**更棒的是**，在Public Hostname里Service可以直接配置内网内的其他主机，不需要在pi上做端口映射/反向代理！

## 三、穿透非http/https服务
群辉的桌面drive使用的端口为6690，用http/https服务不能完成同步任务。[这篇文章](https://hackfang.me/nas-cloudflare-tunnel)给了解决方案。官方给出的解决方法参照[该链接](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/use-cases/)。
### 3.1 cloudflare tunnel仪表盘设置
在Public Hostname中添加一条记录：
```shell
Public hostname: pcdrive.example.com
Server: TCP://DS_local_IP:6690
```
### 3.2 链接的客户端（使用桌面drive的电脑）设置
桌面也需要一个cloudflared来和cloudflare之间创建一个warp连接，桌面从[此链接](https://github.com/cloudflare/cloudflared/releases)下载cloudflared。
安装完成之后运行**（注意此处类型选择TCP）**：
```shell
cloudflared access tcp --hostname pcdrive.example.com --url localhost:6690
```
再在桌面drive上创建一个连接**（注意此处IP写本地localhost）**：
```shell
IP、域名： 127.0.0.1
账号：<username>
密码：<password>
```