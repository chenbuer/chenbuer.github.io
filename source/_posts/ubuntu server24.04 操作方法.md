---
title: ubuntu server24.04 操作方法
date: 2024-9-10 14:35:52
categories: 运维
tags: 网络
---

## 一、网络配置
### 1.1 配置WiFi
配置的password是经过加密的，利用`wpa_passphrase`:
<!--more-->
```shell
~$ sudo wpa_passphrase "wifiuser" "wifipasswd"
network={
        ssid="wifiuser"
        #psk="wifipasswd"
        psk=9d074d88c78d11caa5a8748f2751b88266c683010f272d94708f3064504f7cf1
}
```
修改配置文件：
```shell
network:
    version: 2
    ethernets:
        eth0:
            dhcp4: true
    wifis:
        renderer: networkd
        wlan0:
            access-points:
                wifiuser:
                    password: 9d074d88c78d11caa5a8748f2751b88266c683010f272d94708f3064504f7cf1
            dhcp4: true
            optional: true
```
### 1.2 修改eth0为静态IP
建议在openwrt里面绑定静态地址，不要在这里直接改，防止更换了路由器还得修改之后再接入。

要是一定要修改，如下：
```shell
network:
    version: 2
    ethernets:
      eth0:
        addresses: [192.168.1.100/24]
        routes:
          - to: default
            via: 192.168.1.1
        nameservers:
            addresses: [192.168.1.1]
    wifis:
        renderer: networkd
        wlan0:
            access-points:
                wifiuser:
                    password: 9d074d88c78d11caa5a8748f2751b88266c683010f272d94708f3064504f7cf1
            dhcp4: true
            optional: true
```
### 1.3 使网络配置生效
```shell
sudo netplan apply
```

## 二、scp拷贝文件到服务器
```shell
> scp local_file_name root@server_ip:/path
root@server_ip's password:
Permission denied, please try again.
```
这不是因为路径文件夹没有权限，也不是因为root密码输入错误，更不是sshd服务没开。是因为server默认不允许远程用root身份ssh登录，需要修改`sshd`的配置文件` sudo vi /etc/ssh/sshd_config`：
```shell
PermitRootLogin yes #允许root登录
PermitEmptyPasswords no #不允许空密码登录
PasswordAuthentication yes # 设置是否使用口令验证。
```
配置后重启sshd服务：
```shell
/etc/init.d/ssh restart
```
