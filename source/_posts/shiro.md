---
title: shiro
date: 2018-05-09 01:22:20
categories: JAVA
tags: JAVA
---
### 一、关于shiro
一个shiro的配置案例如下：
<!--more-->
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

### 二、subject.login()发生了什么
[参考](http://blog.51cto.com/luchunli/1830020)

所以最终还是回归到了我们自定义的`doGetAuthenticationInfo(AuthenticationToken authenticationToken)`方法。

### 三、权限验证
(1) 可以简单书写如下：
```java
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authcToken) throws AuthenticationException {  
        UsernamePasswordToken token = (UsernamePasswordToken) authcToken;  
        User user = accountManager.findUserByUserName(token.getUsername());  
        if (user != null) {  
            return new SimpleAuthenticationInfo(user.getUserName(), user.getPassword(), getName());  
        } else {  
            return null;  
        }  
} 
```
但是这时候我们就回有一疑问：这里面都没有验证密码？这个其实是交给`credentialsMatcher`去验证的。所以我们可以自定义一个bean，其实现类继承自`SimpleCredentialsMatcher`，复写`doCredentialsMatch`方法:
```java
public class MyCredentialsMatcher extends SimpleCredentialsMatcher {
    @Override
    public boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info) {
        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
        String password = String.valueOf(usernamePasswordToken.getPassword());
        String dbpassword = (String) info.getCredentials();
        return this.equals(password, dbpassword);
    }
}
```
[参考一](http://www.cnblogs.com/chyu/p/5958720.html)--[参考二](http://jinnianshilongnian.iteye.com/blog/2022468)

最后MyCredentialsMatcher这个bean注入自定义Realm中。

(2) 也可以不用自定义`MyCredentialsMatcher`，直接在`doGetAuthenticationInfo`把密码校验给校验了。zheng项目就是这么做的。

### 四、realm和subject的个人理解
realm可以理解为安全数据源。

自定义的realm，如继承自`AuthorizingRealm`，实现了`doGetAuthenticationInfo(AuthenticationToken authenticationToken)`(这个方法之后用户就获得了用户是否验证通过信息保存到subject对象中)和`doGetAuthorizationInfo(PrincipalCollection principalCollection)`(这个方法之后，就可以检查用户是否具备了某些权限保存到了subject对象中)。在业务代码中我们可以这样判断authenticate和authorization结果：
```java
Subject subject = SecurityUtils.getSubject();
subject.hasRole("admin");       // 检查用户有没有"admin"这个角色
subject.isAuthenticated();      // 检查这个用户有没有鉴权通过
```
> 1、首先调用 Subject.isPermitted*/hasRole*接口，其会委托给 SecurityManager，而
SecurityManager 接着会委托给 Authorizer；

> 2、Authorizer 是真正的授权者，如果我们调用如 isPermitted(“user:view”)，其首先会**通过`PermissionResolver` 把字符串转换成相应的 Permission 实例**；

> 3、在进行授权之前，其会调用相应的 Realm 获取 Subject 相应的角色/权限用于匹配传入的
角色/权限；

> 4、Authorizer 会判断 Realm 的角色/权限是否和传入的匹配，如果有多个 Realm，会委托给
ModularRealmAuthorizer 进行循环判断，如果匹配如 isPermitted*/hasRole*会返回 true，否则返回 false 表示授权失败

关于授权：
`public abstract class AuthorizingRealm extends AuthenticatingRealm implements Authorizer, Initializable, PermissionResolverAware,  RolePermissionResolverAware`可见AuthorizingRealm实现了Authorizer，所有就有了isPermitted/hasRole方法

### 五、SecurityManager和SecurityUtils设置
```java
public void test() {

        DefaultSecurityManager securityManager = new DefaultSecurityManager();

        // 1.设置authenticator
        ModularRealmAuthenticator authenticator = new ModularRealmAuthenticator();
        authenticator.setAuthenticationStrategy(new AtLeastOneSuccessfulStrategy());
        securityManager.setAuthenticator(authenticator);

        // 2.设置authorizer
        ModularRealmAuthorizer authorizer = new ModularRealmAuthorizer();
        authorizer.setPermissionResolver(new WildcardPermissionResolver());
        securityManager.setAuthorizer(authorizer);

        // 3.设置Realm
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName("com.mysql.jdbc.Driver");
        ds.setUrl("jdbc:mysql://localhost:3306/shiro");
        ds.setUsername("root");
        ds.setPassword("");

        JdbcRealm jdbcRealm = new JdbcRealm();
        jdbcRealm.setDataSource(ds);
        jdbcRealm.setPermissionsLookupEnabled(true);
        securityManager.setRealms(Arrays.asList((Realm) jdbcRealm));

        // !!!!至此，sercurityManager就设置完成了
        //将SecurityManager设置到SecurityUtils 方便全局使用
        SecurityUtils.setSecurityManager(securityManager);

        Subject subject = SecurityUtils.getSubject();

        UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
        subject.login(token);

        Assert.assertTrue(subject.isAuthenticated());
    }
    ```