---
title: springMVC与Mybatis整合时一些常用的配置文件
date: 2017-03-27 22:20:13
categories: JAVA
tags: [mybatis,spring]
---
主要包含了:
- web.xml
- spring的beans配置文件
- springMVC的配置文件
- Mybatis配置文件
- log4j的配置文件
<!-- more -->
### 一、web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	id="WebApp_ID" version="2.5">
	<display-name>MyBatisPro05</display-name>
	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>

	<!-- Spring配置文件 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>
	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<async-supported>true</async-supported>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- Spring监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- 添加对springmvc的支持 -->
	<servlet>
		<servlet-name>springMVC</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring-mvc.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
		<async-supported>true</async-supported>
	</servlet>
	<servlet-mapping>
		<servlet-name>springMVC</servlet-name>
		<url-pattern>*.do</url-pattern>
	</servlet-mapping>
</web-app>
```

### 二、spring配置文件applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xmlns:p="http://www.springframework.org/schema/p"  
    xmlns:aop="http://www.springframework.org/schema/aop"   
    xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:jee="http://www.springframework.org/schema/jee"  
    xmlns:tx="http://www.springframework.org/schema/tx"  
    xsi:schemaLocation="    
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd  
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd  
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd  
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd  
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">    
        
	<!-- 自动扫描 -->
	<context:component-scan base-package="com.java1234.dao" />
	<context:component-scan base-package="com.java1234.service" />
	
	<!-- 配置数据源 -->
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
		<property name="url" value="jdbc:mysql://localhost:3306/db_mybatis"/>
		<property name="username" value="root"/>
		<property name="password" value="123456"/>
	</bean>

	<!-- 配置mybatis的sqlSessionFactory -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<!-- 自动扫描mappers.xml文件 -->
		<property name="mapperLocations" value="classpath:com/java1234/mappers/*.xml"></property>
		<!-- mybatis配置文件 -->
		<property name="configLocation" value="classpath:mybatis-config.xml"></property>
	</bean>

	<!-- DAO接口所在包名，Spring会自动查找其下的类 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.java1234.dao" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>

	<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	
	<!-- 配置事务通知属性 -->  
    <tx:advice id="txAdvice" transaction-manager="transactionManager">  
        <!-- 定义事务传播属性 -->  
        <tx:attributes>  
            <tx:method name="insert*" propagation="REQUIRED" />  
            <tx:method name="update*" propagation="REQUIRED" />  
            <tx:method name="edit*" propagation="REQUIRED" />  
            <tx:method name="save*" propagation="REQUIRED" />  
            <tx:method name="add*" propagation="REQUIRED" />  
            <tx:method name="new*" propagation="REQUIRED" />  
            <tx:method name="set*" propagation="REQUIRED" />  
            <tx:method name="remove*" propagation="REQUIRED" />  
            <tx:method name="delete*" propagation="REQUIRED" />  
            <tx:method name="change*" propagation="REQUIRED" />  
            <tx:method name="get*" propagation="REQUIRED" read-only="true" />  
            <tx:method name="find*" propagation="REQUIRED" read-only="true" />  
            <tx:method name="load*" propagation="REQUIRED" read-only="true" />  
            <tx:method name="*" propagation="REQUIRED" read-only="true" />  
        </tx:attributes>  
    </tx:advice>  
  
    <!-- 配置事务切面 -->  
    <aop:config>  
        <aop:pointcut id="serviceOperation"  
            expression="execution(* com.java1234.service.*.*(..))" />  
        <aop:advisor advice-ref="txAdvice" pointcut-ref="serviceOperation" />  
    </aop:config>  

</beans>
```

### 三、sringMVC配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<beans xmlns="http://www.springframework.org/schema/beans"    
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
    xmlns:p="http://www.springframework.org/schema/p"  
    xmlns:aop="http://www.springframework.org/schema/aop"   
    xmlns:context="http://www.springframework.org/schema/context"  
    xmlns:jee="http://www.springframework.org/schema/jee"  
    xmlns:tx="http://www.springframework.org/schema/tx"  
    xsi:schemaLocation="    
        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd  
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd  
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd  
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd  
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">    

	<!-- 使用注解的包，包括子集 -->
	<context:component-scan base-package="com.java1234.controller" />

	<!-- 视图解析器 -->
	<bean id="viewResolver"
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/" />
		<property name="suffix" value=".jsp"></property>
	</bean>

</beans>  
```

### 四、mybatis的配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 别名 -->
	<typeAliases>
		<package name="com.java1234.entity"/>
	</typeAliases>
</configuration>
```

### 五、log4j配置文件
```xml
log4j.rootLogger=info,appender1,appender2

log4j.appender.appender1=org.apache.log4j.ConsoleAppender 

log4j.appender.appender2=org.apache.log4j.FileAppender 
log4j.appender.appender2.File=C:/logFile.txt
 
log4j.appender.appender1.layout=org.apache.log4j.TTCCLayout
log4j.appender.appender2.layout=org.apache.log4j.TTCCLayout  
```

