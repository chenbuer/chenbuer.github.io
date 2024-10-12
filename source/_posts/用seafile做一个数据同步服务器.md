---
title: 用seafile做一个数据同步服务器
date: 2024-9-30 16:50:52
categories: 运维
tags: 网络
---

利用了一个瘦客户机（dell Wyse N03D），处理器n2807，是一个双核处理器。利用ubuntu server和seafile完成同步任务。
## 一、安装系统
这是一台瘦终端，硬件较老，对UEFI支持不完善。但是机器是一个64bit的处理器，还是想用64位的ubuntu server。
<!--more-->
> 问题： 在安装了ubuntu server 24.04之后，进不去BIOS了。扣掉主板电池，`Advanced -> Boot From USB`设置为`Enable`，`Boot Mode`设置为`Both`，可以制作ubuntu server 24.04到msata上，但是又进不去bios了。


**原因分析**：
![u2807主机bios](/images/用seafile做一个数据同步服务器/u2807主机bios.png)
这个主机太老，**BIOS版本太低**。查看`Boot Mode`的值选项，选择`Legacy Common`，再去找ubuntu server还支持lagecy/BIOS启动方式的版本：
![20.04.01_support_legacy](/images/用seafile做一个数据同步服务器/20.04.01_support_legacy.png)
参考[官方链接](https://cdimage.ubuntu.com/ubuntu-legacy-server/releases/20.04/release/)

**处理方法**
- 扣掉主板电池，恢复BIOS默认值
- 设置`Boot From USB`设置为`Enable`，`Boot Mode`设置为`Legacy Common`
- 下载`ubuntu-20.04.1-legacy-server-amd64.iso`，用`rufus`将一个u盘做成系统盘。**分区类型：MRR**，因为分区类型为GPT，只支持UEFI引导
- 小主机设置为U盘boot优先，给msata做系统

## 二、软件安装
### 2.1 gost
得先配置号gost，不然安装docker都无法安装。这个不需要`systemctl enable gost.service`
### 2.2 docker
- 安装docker，docker compose
- 配置docker守护进程代理，配置`/etc/systemd/system/docker.service.d/http-proxy.conf`
### 2.3 安装seafile服务
- [在ubuntu server 24.04 上安装seafile](https://cloud.seafile.com/published/seafile-manual-cn/docker/%E7%94%A8Docker%E9%83%A8%E7%BD%B2Seafile.md)
- [使用教程](https://www.bilibili.com/video/BV13r4y187cL)
### 2.4 安装seafile客户端
- win7
由于[新版的qt不支持win7](https://forum.seafile.com/t/in-which-version-of-client-for-windows-support-for-windows-7-was-disabled/21811)，从别人备份的获取到了客户端：[地址一](https://bbs.seafile.com/t/topic/19923)，[地址二](https://www.iteait.com/archives/1204)。
- win10
从[官网](https://www.seafile.com/download/)下载。不要下载挂载盘客户端，这个不落到自己的电脑，而是直接编辑在云端。

## 三、配置定时重启
防止长时间系统卡主，`crontab -e`添加一行定时任务：
```shell
0 3 1 * * /usr/sbin/reboot
```
每个月1号凌晨3点重启一次

## 四、使用
### 4.1 创建用户
管理员登录，`系统管理->用户`
### 4.2 iphone上传下载失败
一直显示“排队上传中”或者“排队下载中”。参考[该配置](https://blog.csdn.net/weixin_40559937/article/details/102544313)，管理员登入->系统管理->设置->设置`SERVICE_URL`或`FILE_SERVER_ROOT`为内网服务器地址。

### 4.3 不显示绿色小图标
重启。要是重启也不行，就需要修改注册表，参考[这篇文章](https://bbs.seafile.com/t/seafile/1521/3)：
- 打开运行,输入 regedit.
- 移动到`HKEY_LOCAL_MACHINE->SOFTWARE->Microsoft->Windows->CurrentVersion->Explorer->ShellIconOverlayIdentifiers`
- 发现有6个 Seafile 开头的文件夹,将这些文件夹重命名,前面添加多几个空格.
- 打开任务管理器,找到"windows 资源管理器",右键,重新启动.
- OK 小绿标回来了.
### 4.4 重置密码
用管理员用户登录，`系统管理->用户`，找到那个用户，重置密码，确认之后，上方有一个弹框，会给出新的密码。
### 4.5增加管理员
```shell
docker exec -it seafile bash
# 进入了seafile容器
cd seafile-server-latest
./reset-admin.sh
```
其实是添加管理员，即使是email和已有的管理员一致，它也是新增一个同样email的管理员，没测试出机制，不建议这样操作。
### 4.6 设置邮件通知
[设置参考](https://cloud.seafile.com/published/seafile-manual-cn/config/sending_email.md)，不设置了。
### 4.7 文件被误删
登录seafile网页端；进入同步的文件夹；有一个时钟的同步，点击进入修改/同步历史；找到被误删前面的镜像文件；选择“还原”。
[参考操作方法](https://cloud.seafile.com/published/seafile-user-manual/web_client/file_history_recycle.md)

## 五、小管家软件&数据恢复
1. seafile客户端恢复所有数据；
2. 安装小管家软件，软件为trxgjsoft.exe 安装包，从[该下载页](https://pc.qq.com/detail/0/detail_12200.html)下载；
3. 把seafile同步到的数据全量拷贝到新的安装目录；
4. 创建个人用户，参考原来的账户信息，如“xx京东家电专卖店”等；
5. 将u盘（加密狗）插到新电脑；
6. 重新打开软件。