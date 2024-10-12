---
title: ubuntu server 操作方法
date: 2024-9-10 14:35:52
categories: 运维
tags: 网络
---
**主要使用的是20.04legacy版本，或24.04LTS版本**
> 用`树莓派镜像烧录器(Raspberry Pi Imager)`烧录系统，在烧录系统的时候就可以配置用户名、密码、主机名、wifi等，这样就不需要先用键盘/显示器登录。
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
## 四、本地gost代理中转
### 4.1 配置
在windows上直接是`gost.exe -L :6666 -F https://www.proxyServer.xyz:443?auth=base64(user:passwod)`，其实也可以利用`./gost -L :6666 -F https://username:passwd@www.proxyServer.xyz:443 -O yaml`导出为一个配置文件写在`/opt/gost/config.yaml`中：
```yaml
services:
  - name: service-0
    addr: :6666
    handler:
      type: auto
      chain: chain-0
    listener:
      type: tcp
chains:
  - name: chain-0
    hops:
      - name: hop-0
        bypass: bypass-0
        nodes:
          - name: node-0
            addr: www.proxyServer.xyz:443
            connector:
              type: http
              auth:
                username: username
                password: passwd
            dialer:
              type: tls
```
然后在`/usr/lib/systemd/system/gost.service`配置服务：
```shell
[Unit]
Description=Gost Service
After=network.target
Wants=network.target

[Service]
Type=simple
User=root
ExecStart=/opt/gost/gost -C /opt/gost/config.yaml
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
### 4.2 检查服务是否开机自启动
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
### 5.1 docker需要代理
#### 5.1.1 配置一个gost服务
```shell
/opt/gost/gost -L :6666 -F proxy?auth=< auth >
```
#### 5.1.2 配置环境变量
在`sudo vi /etc/profile`中配置环境变量：
```shell
export http_proxy=http://127.0.0.1:6666
export https_proxy=http://127.0.0.1:6666
```
使生效：
```shell
source /etc/profile
```
### 5.2 安装docker和docker compose
**这里需要先设置http_proxy和https_proxy环境变量，不然会访问超时。**
- 卸载原来的docker相关包，防止冲突
```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
- 更新
```shell
sudo apt update
sudo apt upgrade
sudo apt full-upgrade
```
- 首先，安装必要的证书并允许 apt 包管理器使用以下命令通过 HTTPS 使用存储库
```shell
sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg lsb-release
```
- 运行下列命令添加 Docker 的官方 GPG 密钥
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
- 再添加 Docker 官方库
```shell
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- 证书安装好了再update，因为docker用的是https
```shell
sudo apt update
```
- 开始安装docker（耗时长）
```shell
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
- 安装其他版本参考教程
- 验证 Docker 服务是否在运行
```shell
sudo systemctl status docker
```
- 使 Docker 服务在每次重启时自动启动
```shell
sudo systemctl enable docker
```
- 参照 5.1 给dokcer守护进程加代理

### 5.3 配置docker守护进程的proxy
### 问题：配置好了之后`sudo docker search gost`仍然报错
报错内容：
```shell
sudo HTTP_PROXY=http://127.0.0.1:6666 HTTPS_PROXY=http://127.0.0.1:6666 docker search gost
[sudo] password for buer:
Error response from daemon: Get "https://index.docker.io/v1/search?q=gost&n=25": dial tcp 104.16.252.55:443: i/o timeout
```
原因（来自AI，后面也找到了依据，在下面的参考文档里）：***Docker 守护进程的代理设置***:命令行中设置的代理环境变量只对当前命令生效,不会影响 Docker 守护进程。Docker 守护进程需要单独配置代理。为 Docker 守护进程配置代理:
- 编辑 Docker 服务文件(通常在 `/etc/systemd/system/docker.service.d/http-proxy.conf`),添加:
```shell
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:6666"
Environment="HTTPS_PROXY=http://127.0.0.1:6666"
```
- 然后重启 Docker 服务:
```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```
后面用`docker-compose`命令时，用`sudo docker compose `去执行
### 5.4 参考文章
- [如何在 Ubuntu 22.04 LTS 中安装 Docker 和 Docker Compose](https://www.cnblogs.com/carmi/p/17939025)
- [安装docker的官方文档](https://docs.docker.com/engine/install/ubuntu/)
- [如何配置docker通过代理服务器拉取镜像](https://www.lfhacks.com/tech/pull-docker-images-behind-proxy/)

## 六、挂载设备
### 6.1 挂载硬盘

### 6.2 挂载samba服务
- 测试
```shell
sudo mount -t cifs //samba_ip/path_to_floder /mnt/ds/media -o username=jellyfin,password=my_passwd,vers=2.0
```
用`ls /mnt/ds/media`查看是不是都挂载成功了，要是可以正确列出文件，则samba服务没问题。`sudo umount /mnt/ds/media`先卸载掉。

- 防止重启之后挂载信息丢失
编辑`/etc/fstab`，末尾增加一行
```shell
//samba_ip/path_to_floder /mnt/ds/media cifs username=jellyfin,passwormy_passwd,vers=2.0,uid=1000,gid=1000,_netdev,x-systemd.automount,x-systemd.idle-timeout=60,x-systemd.device-timeout=5s,x-systemd.mount-timeout=5s 0 0
```
编辑之后`sudo systemctl daemon-reload & sudo mount -a`去挂载

## 七、修改ubuntu server登录方式
### 7.1 默认登录方式
需要在server的.ssh/authorized_keys里面添加ssh client的publickey才能登录，不然不允许ssh登录。
### 7.2 修改配置文件
修改`/etc/ssh/sshd_config.d/60-cloudimg-settings.conf`中的`PasswordAuthentication yes`，若是没有这个文件就到`/etc/ssh/sshd_config`中找。修改之后重启sshd服务`sudo systemctl restart sshd`

## 八、笔记本合上盖子之后休眠
[参考博客](https://blog.csdn.net/no1xium/article/details/107633520)
- 修改配置文件`/etc/systemd/logind.conf`中的`HandleLidSwitch`选项为`ignore`:
```shell
#HandleLidSwitch=suspend 
HandleLidSwitch=ignore
```
- 重启服务：
```shell
service systemd-logind restart
```

## 九、查看systemctl服务日志
#### systemctl服务启动失败
用命令
```shell
journalctl -u frpc.service -n 50
```
查看日志