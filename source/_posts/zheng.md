---
title: zheng
date: 2018-04-30 13:41:20
categories: JAVA
tags: JAVA
---
### 一. zheng的启动顺序
- **ZhengCmsRpcServiceApplication.main**
- **ZhengUpmsRpcServiceApplication.main**
- **zheng-upms-server(jetty)**
- **zheng-cms-admin(jetty)**

<!--more-->
### 二、权限管理系统
```xml
├── zheng-upms -- 用户权限管理系统
|    ├── zheng-upms-common -- upms系统公共模块
|    ├── zheng-upms-dao -- 代码生成模块，无需开发
|    ├── zheng-upms-client -- 集成upms依赖包，提供单点认证、授权、统一会话管理
|    ├── zheng-upms-rpc-api -- rpc接口包
|    ├── zheng-upms-rpc-service -- rpc服务提供者
|    └── zheng-upms-server -- 用户权限系统及SSO服务端(是一个web工程)[端口:1111]
```

### 三、Example实体类
mybatis-generator自动生成实体类会生成Example实体类，方便查询。具体使用可以参考：
- [参考一](https://www.cnblogs.com/kangping/p/6001519.html)
- [参考二](https://blog.csdn.net/zhemeban/article/details/71901759)
- [参考三](http://www.mybatis.org/generator/generatedobjects/exampleClassUsage.html)

### 四、