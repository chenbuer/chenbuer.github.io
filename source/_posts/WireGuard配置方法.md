---
title: WireGuard配置方法
date: 2024-09-07 19:30:52
categories: 网络
tags: 网络
---


接入家庭局域网有多种方式：[openVPN](https://github.com/OpenVPN/openvpn)、[Tailscale](https://github.com/tailscale/tailscale)、[frp](https://gofrp.org/zh-cn/)、[nps](https://github.com/ehang-io/nps)、[ZeroTier](https://www.zerotier.com/)等等。但若是可以拨号获得一个公网IP，则不需要公网的服务器搭建一个转发服务，构建VPN局域网。这里选择的是[WireGuard](https://www.youtube.com/watch?v=F8z74oE71Gg)。
<!--more-->
### 一、测试网络结构
![测试网络](/images/WireGuard配置方法/测试网络.png)
- 公网网段： 172.16.0.0/24
- br-lan网段：10.0.0.0/24
- WireGuard网段：192.168.100.0/24

### 二、配置WireGuard
大概过程分为：
- openwrt中安装WireGuard：
```shell
    opkg update
    opkg install luci-proto-wireguard luci-app-wireguard wireguard kmod-wireguard wireguard-tools
```
- 网络 -> 接口 ，创建wg0接口，接口协议为 `WireGuard VPN`
- 编辑wg0接口，**与br-lan在同一个防火墙区域**
- 配置peer信息，即允许接入的peer有哪些
- 配置其他的peer端，获取配置后接入

#### 所有配置：
1. **openwrt**中的配置，配置后在`/etc/config/network`中查看：
```shell
config interface 'wg0'
        option proto 'wireguard'
        option private_key '《openwrt的private key》'
        option listen_port '8890'
        option mtu '1250'
        option nohostroute '1'
        option ip4table '21'
        list addresses '192.168.100.1/32'

config wireguard_wg0 'wgserver'
        option description 'iPhone'
        option public_key 'e++EP53PQnMQhMcJ0dJd9cvnGIl+gxJEx9a8EtSJuCY='
        option preshared_key 'AO5DXGfwDwm0WgQqTuPIvHpVeOwAdZeUOG0qQXhFPzI='
        option persistent_keepalive '25'
        option route_allowed_ips '1'
        list allowed_ips '192.168.100.0/24'

config wireguard_wg0
        option description 'pc'
        option public_key 'O0q757lZh7+cG5Q1js7wx9df1936kpH5NQ9TWAJEqRk='
        option preshared_key 's4ZLhXba8bEEJW+Zc8T9qDlbu2f1I6bRyejBrMWCbS4='
        option route_allowed_ips '1'
        option persistent_keepalive '25'
        list allowed_ips '192.168.100.3'
```
2. **iPhone**的配置：
在openwrt配置的allowed_ips为'192.168.100.0/24'，但是我直接指定为192.168.100.2
```shell
[Interface]
PrivateKey = 《iPhone的private》
Address = 192.168.100.2
# ListenPort not defined
DNS = 10.0.0.1

[Peer]
PublicKey = AkVgVs2lcYUs5UtpD04RJPBSbnpKklzZQ4/3AK9uTH0=
PresharedKey = AO5DXGfwDwm0WgQqTuPIvHpVeOwAdZeUOG0qQXhFPzI=
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint = 172.16.0.6:8890
PersistentKeepAlive = 25
```
3. **pc**的配置：
为了分流，配置AllowedIPs 为 192.168.10.0/0, 10.0.0.0/0   这样，pc上只有这两个网段的流量经过WireGuard，其他不从这里走。
```shell
[Interface]
PrivateKey = 《pc的privateky》
Address = 192.168.100.3
# ListenPort not defined
DNS = 10.0.0.1

[Peer]
PublicKey = AkVgVs2lcYUs5UtpD04RJPBSbnpKklzZQ4/3AK9uTH0=
PresharedKey = s4ZLhXba8bEEJW+Zc8T9qDlbu2f1I6bRyejBrMWCbS4=
AllowedIPs = 192.168.10.0/0, 10.0.0.0/0
Endpoint = 172.16.0.6:8890
PersistentKeepAlive = 25
```

### 三、遇到的问题
由于WireGuard使用人数不多
#### 问题1：配置完成后无法正确握手
防火墙问题，需要配置openwrt允许wireguard来握手。`网络 -> 防火墙 -> 通信规则 -> 添加`：
```
    名称:allow-wireguard-handshake
    协议：UDP
    源区域:wan
    目标区域：设备
    目标地址：172.16.0.6  #openwrt的wan口地址，相当于拨号获得
    目标端口：8890  # wireguard配置在8890端口
```

#### 问题2：可以正确握手，但是无法利用局域网br-lan网络
1. 在openwrt命令行中查看路由信息：
```shell
default via 172.16.0.1 dev eth0  src 172.16.0.6
10.0.0.0/24 dev br-lan scope link  src 10.0.0.1
172.16.0.0/24 dev eth0 scope link  src 172.16.0.6
```
没有192.168.100.0/24网段的路由信息，在openwrt上运行：
```shell
ip route add 192.168.100.0/24 dev wg0
```

2. 发现一重启网络（`/etc/init.d/network restart`）配置就丢失，在luci配置静态IPv4路由：
```
    接口（Interface）: wg0
    路由类型（Route Type）: unicast
    目标（Target）: 192.168.100.0/24
    网关（Gateway）: 留空
    表（Table）: 使用默认的 main
    源地址（Source Address）: 留空
    On-Link 路由（On-Link Route）: 关闭
```
配置信息保存在`/etc/config/network`中，重启Network服务，不管是`ip route`还是`ip route show table 254`，都不显示配置的信息。怀疑openwrt版本问题。

3. 在`/etc/rc.local`脚本中添加这条命令：
```shell
ip route add 192.168.100.0/24 dev wg0
```
#### 问题3：pc可以访问，但是iPhone访问不了
是因为我原来的两个公网peer，一个明确了`允许的IP`为`192.168.100.3`。另一个没配置，就觉得他会自动获取一个地址，其实还是需要配置允许的IP为`192.168.100.0/24`
