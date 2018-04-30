---
title: zheng
date: 2018-04-30 13:41:20
categories: JAVA
tags: JAVA
---
### 一. zheng的启动顺序
![启动顺序](https://github.com/chenbuer/zheng/blob/master/project-bootstrap/zhengStart.png)

<!--more-->
### 二、权限管理系统
```xml
├── zheng-upms -- 用户权限管理系统
|    ├── zheng-upms-common -- upms系统公共模块
|    ├── zheng-upms-dao -- 代码生成模块，无需开发
|    ├── zheng-upms-client -- 集成upms依赖包，提供单点认证、授权、统一会话管理
|    ├── zheng-upms-rpc-api -- rpc接口包
|    ├── zheng-upms-rpc-service -- rpc服务提供者
|    └── zheng-upms-server -- 用户权限系统及SSO服务端[端口:1111]
```