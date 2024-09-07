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
由于WireGuard使用人数不多，很多问题网络上没有完整解释。
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

### 四、疑问
> 以下问答基于AI找到的答案，不负责真实性权威性

#### 1. 路由策略`ip route add 192.168.100.0/24 dev wg0`是什么意思？为什么其他接口没配置，但是WireGuard需要自己手动配置？
- 这条命令告诉系统:要到达192.168.100.0到192.168.100.255范围内的任何IP地址，都应该通过wg0接口发送流量。
- wg0接口通常与WireGuard（一种现代VPN协议）相关联。这表明该路由可能是为了通过WireGuard VPN隧道将流量定向到特定网络。
- 这种设置常见于VPN配置中，用于将特定网段的流量路由through VPN隧道。

#### 2. 所有的peer获取到隧道内的局域网地址都没有配置网关；请求lan网段的流量，根据上述路由也只是表明wg0发送，它往哪个端口发送？
- 关于peers不配置网关：
在WireGuard配置中，通常不需要为peers明确指定网关。WireGuard使用公钥加密和隧道技术，不同于传统VPN需要指定网关的方式。
- 关于"往哪里发送"的问题，当添加 `ip route add 192.168.100.0/24 dev wg0` 这条路由时：
    - 系统知道要将目标为192.168.100.0/24的流量发送到wg0接口。
    - WireGuard然后根据其配置，决定使用哪个peer的公钥来加密数据。
    - 加密后的数据被封装在UDP包中，通过您的物理网络接口发送到配置的peer的公网IP和端口。

这种方法允许WireGuard在不需要传统意义上的"网关"的情况下，建立安全的点对点连接。

#### 3. 为什么lan区域没有任何终端作为peer也能直接访问？
- WireGuard隧道建立后，远程peer实际上成为了本地网络的一个虚拟成员。
- 由于WireGuard接口和br-lan在同一防火墙区域，来自WireGuard的流量被视为"本地"流量。
- 路由器的配置允许这些"本地"流量访问br-lan网段的服务。
- 安全考虑：
    - 这种配置提供了便利性，但也需要谨慎管理访问控制。
    - 可以通过WireGuard的AllowedIPs配置和额外的防火墙规则来限制访问。

#### 4. WireGuard原理
1. **WireGuard接口（wg0）的角色**：
   - wg0是一个虚拟网络接口，作为WireGuard隧道的端点。
   - 它封装和解封装通过WireGuard隧道传输的数据包。

2. **路由决策过程**：
   - 当系统需要发送数据包到192.168.100.0/24网段时，它会查询路由表。
   - 路由表指示这些数据包应该通过wg0接口发送。
   - 数据包被传递给wg0接口处理。

3. **WireGuard的处理**：
   - wg0接口接收到数据包后，WireGuard会查看其内部配置。
   - 它会根据目标IP地址决定使用哪个peer的公钥来加密数据包。
   - 加密后的数据包被封装在UDP数据包中。

4. **数据包的实际发送**：
   - 封装后的UDP数据包会通过系统的默认路由（通常是物理网络接口）发送出去。
   - 数据包的目标是WireGuard配置中指定的peer的公网IP地址和端口。

5. **Peer端的处理**：
   - 对端的WireGuard服务接收到UDP数据包后，解密并处理内部的原始数据包。
   - 如果目标IP在对端的允许IP列表中，数据包会被转发到对端的本地网络。

这个过程展示了WireGuard如何在不需要传统网关的情况下，利用加密和隧道技术实现安全的点对点通信。

#### 5. 所以peer的每条往lan区域的流量都是先广播，到达了路由器就知道了交给wg0，然后再转发吗？不然其他peer也不知道我要往路由上的peer发送啊

参照**WireGuard数据包流动过程**，再回答具体问题：

1. **不是广播**：WireGuard不使用广播机制。每个peer的流量都是经过精确路由的。

2. **路由决策**：每个peer在发送数据包时，首先查看自己的路由表。如果目标IP属于WireGuard路由的网段，数据包就会被发送到WireGuard接口（如wg0）。

3. **Peer选择**：WireGuard接口根据配置（特别是`AllowedIPs`字段）决定使用哪个peer的公钥来加密数据。

4. **路由器角色**：当加密的数据包到达路由器时，路由器知道这是WireGuard流量，因为它是发往WireGuard监听的UDP端口的。

5. **转发过程**：路由器解密数据包后，根据目标IP和自己的路由表决定如何转发。如果目标是LAN区域，它会直接转发到相应的接口。

6. **Peer间通信**：其他peer不需要"知道"您要往路由器上的peer发送。每个peer只需要知道自己的直接连接对象（通常是路由器的WireGuard接口）。

所以，整个过程是基于配置和路由表的精确导向，而不是依赖广播。这就是为什么WireGuard能够高效且安全地工作，同时允许复杂的网络拓扑。


#### 6. WireGuard数据包流动过程
1. **初始路由决策**:
   - 当一个peer要发送数据包时，它首先查看自己的路由表。
   - 路由表中包含了通过WireGuard接口（如wg0）可达的网段信息。

2. **WireGuard接口处理**:
   - 如果目标IP属于WireGuard路由的网段，数据包被传递给WireGuard接口。
   - WireGuard不使用广播，而是点对点加密通信。

3. **Peer选择**:
   - WireGuard查看其配置，确定哪个peer负责目标IP地址。
   - 这基于每个peer配置中的`AllowedIPs`字段。

4. **加密和封装**:
   - 数据包被使用目标peer的公钥加密。
   - 加密后的数据被封装在UDP数据包中。

5. **发送到路由器**:
   - 封装后的UDP包通过物理网络接口发送到路由器。
   - 路由器接收到这个UDP包，识别为WireGuard流量。

6. **路由器处理**:
   - 路由器解封装WireGuard数据包。
   - 根据目标IP和路由表，决定如何转发。

7. **转发到LAN**:
   - 如果目标是LAN区域，路由器直接将数据包转发到相应的LAN接口。

8. **返回路径**:
   - 响应遵循类似的过程，但方向相反。
   - LAN设备的响应通过路由器，再通过WireGuard隧道返回到原始peer。

这个过程是自动的，不需要广播。每个peer和路由器都基于其配置和路由表做出决策。
