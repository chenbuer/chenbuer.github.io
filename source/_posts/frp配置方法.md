---
title: frp配置方法
date: 2024-9-3 20:35:52
categories: 网络
tags: 网络
---

将局域网内的服务暴露出去有几种方法：
1. 在路由器中配置为dmz区，就是将整个服务器所有接口全都暴露出去，较简单；
2. 有些路由器不支持dmz，只支持**端口转发**，该方法指定暴露出去的服务器端口/具体服务；
3. 还有就是frp、nps、[ngrok](https://blog.csdn.net/u011886447/article/details/73268407)

frp配置方法就是这篇文章的介绍内容。
<!--more-->
### 二、frp配置方法
frp配置在
[项目地址](https://github.com/fatedier/frp)
#### 2.1 openwrt-frpc
是c-s架构，由于openwrt自带的软件包更新不及时，查看最新的openwrt-frp：（下载的是`aarch64_generic`版本）
```shell
wget https://github.com/kuoruan/openwrt-frp/releases/download/v0.57.0-1/frpc_0.57.0-r1_aarch64_generic.ipk
opkg install frpc_0.57.0-r1_aarch64_generic.ipk
```

luci界面：参考[这篇文章](https://hwhloveslife.com/?p=20)，他强调了，luci-app-frpc请使用1.4-2版本：
```shell
wget https://istore.linkease.com/repo/all/nas_luci/luci-app-frpc_1.4-2_all.ipk
wget https://istore.linkease.com/repo/all/nas_luci/luci-i18n-frpc-zh-cn_1.4-2_all.ipk
opkg install luci-app-frpc_1.4-2_all.ipk
opkg install luci-i18n-frpc-zh-cn_1.4-2_all.ipk
```
经过试用，这个版本的luci界面**配置生效，且有日志**！

#### 2.2 服务器端
也下载0.57版本：
```shell
wget -uv https://github.com/fatedier/frp/releases/download/v0.57.0/frp_0.57.0_linux_amd64.tar.gz
```
配置：
```toml
bindAddr = "0.0.0.0"
bindPort = 7000
kcpBindPort = 7000

webServer.addr = "0.0.0.0"
webServer.port = 7001
webServer.user = "username"
webServer.password = "password"

log.to = "/opt/frp/log/frps.log"
log.level = "info"
log.maxDays = 3

auth.method = "token"
auth.token = "suiji_token"

allowPorts = [
  { start = 3333, end = 4444},
]
```
配置frps的服务(路径：`/usr/lib/systemd/system/frps.service`)：
```shell
[Unit]
Description=Frps Service
After=network.target network-online.target
Wants=network-online.target

[Service]
Type=simple
User=root
ExecStart={FRPS_HOME}/frps -c {FRPS_HOME}/frps.toml
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```
启动服务：
```shell
systemctl enable frps
systemctl start frps
```


### 三、nsp
[项目地址](https://github.com/ehang-io/nps)
### 四、gost
[项目地址](https://github.com/go-gost/gost)