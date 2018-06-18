---
title: nginx配置
date: 2018-06-19 00:12:52
categories: 运维
tags: 运维
---

### Centos下降前段文件放到了buer家目录下
修改Nginx配置之后，访问报403错误，是访问权限错误。修改方法：

I solved this problem by adding user settings.

in nginx.conf

```xml
worker_processes 4;
user username
```
change the 'username' with linux user name.
<!--more-->
