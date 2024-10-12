---
title: cloudflare动态路由
date: 2024-09-06 9:56:52
categories: 网络
tags: 网络
---
路由器是openwrt，dns解析服务器是cloudflare
## 一、问题
nas黑群晖版本较低，不能直接将nas的5000端口通过`openwrt -> 网络 -> 防火墙 -> 端口转发`直接将服务暴露出去，容易有安全问题。
<!--more-->
所以需要一个VPN服务，在外网可以加入VPN，像在家中访问局域网内的NAS服务一样。

家里又没有固定IP，所以需要ddns来获取到公网ip。通过域名连接+VPN加入家庭局域网。


## 二、cloudflare动态路由
### 2.1 安装软件包
在openwrt的`软件包`中下载`luci-i18n-ddns-zh-cn`。在ddns中`添加新服务`，发现在DDNS服务提供商中并没有cloudflare（`cloudflare.com-v4`）。在`软件包`里搜索安装`ddns-scripts-cloudflare`，或者ssh到openwrt，运行：
```shell
opkg update && opkg install ddns-scripts_cloudflare.com-v4
```
至此，软件包安装完成。
### 2.2设置
添加ddns服务可以参照[这篇文章](https://cloud.tencent.com/developer/article/2179526)。里面介绍了如何在cloudflare中获取token，如何新增ddns服务。若是在局域网中测试，但是`update_cloudflare_com_v4.sh`会检查要更新的IP地址是不是公网IP地址，所以可以将`IP地址来源`设置为URL，`用于检测的 URL`设置为`http://checkip.dyndns.com/`。
### 2.3 ipv6设置

## 三、问题
> 有坑：还是报错：
 093154  WARN : Could not detect 'zone id' for domain.tld: 'home.example.com'
 093154 ERROR : No update send to DDNS Provider

 这是因为读取了域名字段的值，是根据@字符分割，前部分就是HOST，后部分是DOMAIN。参考[这篇文章](https://www.elthon.me/2021/07/01/cloudflare-ddns-in-openwrt-settings-problem)。所以需要将域名设置为`home@expample.com`。

