---
title: frp配置方法
date: 2024-9-20 17:35:52
categories: 运维
tags: 网络
---

将局域网内的服务暴露出去有几种方法：
1. 在路由器中配置为dmz区，就是将整个服务器所有接口全都暴露出去，较简单；
2. 有些路由器不支持dmz，只支持**端口转发**，该方法指定暴露出去的服务器端口/具体服务；
3. 还有就是frp、nps（常年不维护，有漏洞）、[ngrok](https://blog.csdn.net/u011886447/article/details/73268407)

frp配置方法就是这篇文章的介绍内容。
<!--more-->
### 二、frp配置方法
frp配置在：[项目地址](https://github.com/fatedier/frp)

#### 2.1 服务器端/公共访问点
配置：
```toml
bindPort = 7000
vhostHTTPPort = 8080
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

#### 2.2 客户端，需要暴露服务的内网pi
`/opt/frpc/frpc.toml`配置如下：
```ini
serverAddr = "www.proxy.com"
serverPort = 7000

[[proxies]]
name = "myweb"
type = "http"
localIP = "192.168.1.18"
localPort = 8096
customDomains = ["myweb.proxy.com"]

[[proxies]]
name = "pissh"
type = "stcp"
secretKey = "<token>"
localIP = "127.0.0.1"
localPort = 22
```
一共暴露两个服务，一个是内网内其他机器（IP地址为192.168.1.18）的http服务，一个是本机的ssh服务。
- http服务，在公网访问http://myweb.proxy.com:8080 即可。url为customDomains配置，端口为frps配置的vhostHTTPPort
- ssh服务，还需要在客户机上配置文件

#### 2.3 客户端，需要登录ssh的客户端
```toml
serverAddr = "www.proxy.com"
serverPort = 7000

[[visitors]]
name = "test"
type = "stcp"
serverName = "pissh"
secretKey = "<token>"
bindAddr = "127.0.0.1"
bindPort = 6000
```
然后本地就可以ssh登录了：
```shell
ssh -o Port=6000 buer@127.0.0.1
```

#### 2.4 https
```shell
serverAddr = "www.proxy.com"
serverPort = 7000

[[proxies]]
name = "video_web"
type = "https"
customDomains = ["video.proxy.com"]

[proxies.plugin]
type = "https2http"
localAddr = "127.0.0.1:8096"

# HTTPS 证书相关的配置
crtPath = "/opt/frpc/ssl/fullchain.crt"
keyPath = "/opt/frpc/ssl/privkey.key"
hostHeaderRewrite = "127.0.0.1"
requestHeaders.set.x-from-where = "frp"
```
ssl证书我是用letsencrypt获得的，
**linux下生成的证书为fullchain.pem格式，复制到Windows上改成.crt后缀即可**
**linux下生成的证书为privkey.pem格式，复制到Windows上改成.key后缀即可**

#### 2.5 openwrt-frpc
在openwrt上配置，试了几次都没有成功连接上。以下方法也不行。frpc版本和luci本身的问题？
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

### 三、一些技巧
#### systemctl服务启动失败
用命令
```shell
journalctl -u frpc.service -n 50
```
查看日志


### 四、参考资料
- [成功配置了web穿透](https://www.talaxy.site/lets-use-frp/)
- [暴露内网ssh服务（官方教程）](https://gofrp.org/zh-cn/docs/examples/stcp/)
- [暴露内网web服务（官方教程）](https://gofrp.org/zh-cn/docs/examples/vhost-http/)
- https
    - [配置示例博客](https://www.cnblogs.com/shook/p/12790532.html)
    - [暴露内网https服务（官方教程）](https://gofrp.org/zh-cn/docs/examples/https2http/)