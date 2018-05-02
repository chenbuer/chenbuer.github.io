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

### 四、关于shiro
一个shiro的配置案例如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

    <!-- Shiro的Web过滤器 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <property name="securityManager" ref="securityManager"/>
        <property name="loginUrl" value="${zheng.upms.sso.server.url}"/>
        <property name="successUrl" value="${zheng.upms.successUrl}"/>
        <property name="unauthorizedUrl" value="${zheng.upms.unauthorizedUrl}"/>
        <property name="filters">
            <util:map>
                <!--czy:重写authc的过滤器-->
                <entry key="authc" value-ref="upmsAuthenticationFilter"/>
            </util:map>
        </property>
        <property name="filterChainDefinitions">
            <value>
                <!--czy:要是过滤器没有被重写的就是用的DefaultFilter.java中声明的过滤器-->
                /manage/** = upmsSessionForceLogout,authc
                /manage/index = user
                /druid/** = user
                /swagger-ui.html = user
                /resources/** = anon
                /** = anon
            </value>
        </property>
    </bean>

    <!-- 重写authc过滤器 -->
    <bean id="upmsAuthenticationFilter" class="com.zheng.upms.client.shiro.filter.UpmsAuthenticationFilter"/>

    <!-- 强制退出会话过滤器 -->
    <bean id="upmsSessionForceLogout" class="com.zheng.upms.client.shiro.filter.UpmsSessionForceLogoutFilter"/>

    <!-- 安全管理器 -->
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="realms">
            <list><ref bean="upmsRealm"/></list>
        </property>
        <property name="sessionManager" ref="sessionManager"/>
        <property name="rememberMeManager" ref="rememberMeManager"/>
    </bean>

    <!-- realm实现，继承自AuthorizingRealm -->
    <bean id="upmsRealm" class="com.zheng.upms.client.shiro.realm.UpmsRealm"></bean>

    <!-- 会话管理器 -->
    <bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
        <!-- 全局session超时时间 -->
        <property name="globalSessionTimeout" value="${zheng.upms.session.timeout}"/>
        <!-- sessionDAO -->
        <property name="sessionDAO" ref="sessionDAO"/>
        <property name="sessionIdCookieEnabled" value="true"/>
        <property name="sessionIdCookie" ref="sessionIdCookie"/>
        <property name="sessionValidationSchedulerEnabled" value="false"/>
        <property name="sessionListeners">
            <list><ref bean="sessionListener"/></list>
        </property>
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>

    <!-- 会话DAO，可重写，持久化session -->
    <bean id="sessionDAO" class="com.zheng.upms.client.shiro.session.UpmsSessionDao"/>

    <!-- 会话Cookie模板 -->
    <bean id="sessionIdCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <!-- 不会暴露给客户端 -->
        <property name="httpOnly" value="true"/>
        <!-- 设置Cookie的过期时间，秒为单位，默认-1表示关闭浏览器时过期Cookie -->
        <property name="maxAge" value="-1"/>
        <!-- Cookie名称 -->
        <property name="name" value="${zheng.upms.session.id}"/>
    </bean>

    <!-- 会话监听器 -->
    <bean id="sessionListener" class="com.zheng.upms.client.shiro.listener.UpmsSessionListener"/>

    <!-- session工厂 -->
    <bean id="sessionFactory" class="com.zheng.upms.client.shiro.session.UpmsSessionFactory"/>

    <!-- rememberMe管理器 -->
    <bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
        <!-- rememberMe cookie加密的密钥 建议每个项目都不一样 默认AES算法 密钥长度（128 256 512 位）-->
        <property name="cipherKey" value="#{T(org.apache.shiro.codec.Base64).decode('4AvVhmFLUs0KTA3Kprsdag==')}"/>
        <property name="cookie" ref="rememberMeCookie"/>
    </bean>

    <!-- rememberMe缓存cookie -->
    <bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg value="rememberMe"/>
        <!-- 不会暴露给客户端 -->
        <property name="httpOnly" value="true"/>
        <!-- 记住我cookie生效时间 -->
        <property name="maxAge" value="${zheng.upms.rememberMe.timeout}"/>
    </bean>

    <!-- 设置SecurityUtils，相当于调用SecurityUtils.setSecurityManager(securityManager) -->
    <bean class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
        <property name="staticMethod" value="org.apache.shiro.SecurityUtils.setSecurityManager"/>
        <property name="arguments" ref="securityManager"/>
    </bean>

    <!-- 开启Shiro Spring AOP权限注解@RequiresPermissions的支持 -->
    <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" depends-on="lifecycleBeanPostProcessor"/>
    <bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
        <property name="securityManager" ref="securityManager"/>
    </bean>

    <!-- Shiro生命周期处理器-->
    <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>

</beans>
```
可见， 三大模块：securityManager、Realm、subject
其中SecurityManager是链接的主体，讲Realm注入其中，并利用`Subject subject = SecurityUtils.getSubject();`可以获得当前的subject

### 五、lombok包的使用
lombok的可以对POJO进行很多封装，减少了很多重复性代码的书写，常用注解有：
- @Getter
- @Setter
- @Builder
- @Data
- @ToString
[参考](https://segmentfault.com/a/1190000005133786)

[参考](https://blog.csdn.net/mccand1234/article/details/53456411)

### 六、filter和interceptor之间的区别
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

### 七、sso
[参考](https://www.cnblogs.com/ywlaker/p/6113927.html)