---
title: buer系统过程中的问题
date: 2018-07-08 15:03:52
categories: java
tags: java
---

### 一、controller中收不到post发来参数
[参考一](https://blog.csdn.net/qq_35420123/article/details/80674861)

[参考二](https://github.com/axios/axios/blob/master/README.md#using-applicationx-www-form-urlencoded-format)
<!--more-->
解决方法是用qs将参数进行stringify

### 二、vue-router中动态添加路由
用addRoutes方法，调用该方法前后打印router.options.routes是没有用的，都没有变化，但是动态路由其实已经设置进去了。 

另外显示的地方需要在Sidebar/index.vue下，将sidebar的route属性改成想要的值，也就是store中permission中的routers属性。

### 三、阿里云Nginx启动之后无法访问
- 安装Nginx，直接用yum：
```shell
sudo yum install -y nginx
```
- 安装之后，本地curl可以访问，但是远程访问不了，应该是防火墙问题（[参考链接一](https://blog.csdn.net/AKai66/article/details/78245470)  --  [参考链接二](https://blog.csdn.net/u010920327/article/details/78117855)）：

    - 关闭firewall防火墙
    ```shell
    systemctl stop firewalld.service #停止firewall
    
    systemctl disable firewalld.service #禁止firewall开机启动
    ```

    - 启动iptables防火墙
    ```shell
    yum -y install iptables-services # 先安装

    编辑iptables防火墙配置文件（/etc/sysconfig/iptables），开启80端口的访问权限，在 
    -A INPUT -p tcp -m state --state NEW -m tcp--dport 22 -j ACCEPT行的上面或者下面添加-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT

    systemctl restart iptables.service #最后重启防火墙使配置生效

    systemctl enable iptables.service #设置防火墙开机启动
    ```
    
    - 还是访问不了，需要在控制台上打开网络与安全中的安全组，[修改安全组规则](https://www.jianshu.com/p/ec34db456c8b)