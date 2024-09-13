---
title: ubuntu server24.04 操作方法
date: 2024-9-10 14:35:52
categories: 运维
tags: 网络
---

功能：
1. 作为cloudflare tunnel的Connector、配置了个cloudflared.server，接入cloudflare。
2. 作转发节点。配置了个gost.server，这是使用pi能使用docker的前置条件。(PS:不过在群辉配置好环境变量，能连接dockerhub之后，下载了gost，也就不需要它了。属于“过河拆桥”了)
<!--more-->

## 一、网络配置
### 1.1 配置WiFi
配置的password是经过加密的，利用`wpa_passphrase`:
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

## 二、配置镜像源
`sudo vi /etc/apt/sources.list.d/ubuntu.sources`:
```shell
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports
Suites: noble noble-updates noble-backports
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

## Ubuntu security updates. Aside from URIs and Suites,
## this should mirror your choices in the previous section.
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg


# 阿里云
Types: deb
URIs: http://mirrors.aliyun.com/ubuntu/
Suites: noble noble-updates noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```
原来的源不能删除，因为有些deb国内下载不了，比如`sudo apt install docker.io`


## 三、scp拷贝文件到服务器
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
## 四、检查服务是否开机自启动
用命令二其一：
```shell
systemctl is-enabled gost.service
systemctl status gost.service
```
![检查服务是否开机自启动](/images/ubuntu_server24.04_操作方法/检查服务开机自启动.png)
若是disabled，设置开机自启动：
```shell
sudo systemctl enable gost.service
```

## 五、docker
配置一个gost服务，略。
```shell
/opt/gost/gost -L :6666 -F proxy?auth=< auth >
```
在`sudo vi /etc/profile`中配置环境变量：
```shell
export http_proxy=http://127.0.0.1:6666
export https_proxy=http://127.0.0.1:6666
```
使生效：
```shell
source /etc/profile
```
### 问题：配置好了之后`sudo docker search gost`仍然报错
报错内容：
```shell
sudo HTTP_PROXY=http://127.0.0.1:6666 HTTPS_PROXY=http://127.0.0.1:6666 docker search gost
[sudo] password for buer:
Error response from daemon: Get "https://index.docker.io/v1/search?q=gost&n=25": dial tcp 104.16.252.55:443: i/o timeout
```
原因（来自AI）：***Docker 守护进程的代理设置***:命令行中设置的代理环境变量只对当前命令生效,不会影响 Docker 守护进程。Docker 守护进程需要单独配置代理。为 Docker 守护进程配置代理:
- 编辑 Docker 服务文件(通常在 `/etc/systemd/system/docker.service.d/http-proxy.conf`),添加:
```
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:6666"
Environment="HTTPS_PROXY=http://127.0.0.1:6666"
```
- 然后重启 Docker 服务:
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```