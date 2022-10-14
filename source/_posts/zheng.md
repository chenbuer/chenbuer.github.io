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

### 四、lombok包的使用
lombok的可以对POJO进行很多封装，减少了很多重复性代码的书写，常用注解有：
- @Getter
- @Setter
- @Builder
- @Data
- @ToString

[参考](https://segmentfault.com/a/1190000005133786)

[参考](https://blog.csdn.net/mccand1234/article/details/53456411)

### 五、filter和interceptor之间的区别
拦截器是AOP( Aspect-Oriented Programming)的一种实现，底层通过动态代理模式完成。

区别：
- **拦截器**是基于java的反射机制的，而**过滤器**是基于函数回调。
- 拦截器不依赖于servlet容器，而过**滤器依赖于servlet容器**。
- 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
- 拦截器可以访问action上下文、值栈里的对象，而过滤器不能。
- 在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
 > 两者的本质区别：从灵活性上说拦截器功能更强大些，Filter能做的事情，他都能做，而且可以在请求前，请求后执行，比较灵活。Filter主要是针对URL地址做一个编码的事情、过滤掉没用的参数、安全校验（比较泛的，比如登录不登录之类），太细的话，还是建议用interceptor。不过还是根据不同情况选择合适的。
 
[参考1](https://blog.csdn.net/qq_36411874/article/details/53996873)

[参考2](https://www.jianshu.com/p/3e6433ead5c3)

### 六、sso
[参考](https://www.cnblogs.com/ywlaker/p/6113927.html)

### 七、为什么登陆cms的后台，验证之后跳转到upms的后台
因为cms后台就是redirect到upms后台的

### 八、ehcache
[参考1](https://www.jianshu.com/p/5a0669d6305e)

[参考2](http://raychase.iteye.com/blog/1545906)

### 九、关于分布式
可以把分布式系统看做一个以系统，他的应用是因为现在服务系统的计算越来越大，利用分散的主机分散大量请求的处理。

**但是很多的东西却因为分散导致了数据不一致，比如session、比如cache，所以又需要集中处理，集中处理常见的有一个集中服务提供一致性保证，比如利用的redis，但是需要网络IO处理，这样又损耗了一定的性能**