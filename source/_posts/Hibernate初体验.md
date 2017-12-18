---
title: Hibernate初体验
date: 2017-02-11 01:55:52
categories: JAVA
tags: Hibernate
---

### 一、Hibernate的操作流程

> 在一个测试类中表示如下所示：

<!--more-->
```java
package springMVC5;

import info.baitian.db.Students;    
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;
import org.hibernate.service.ServiceRegistry;
import org.hibernate.service.ServiceRegistryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test; 
 
public class TestHibernate {

    private SessionFactory sessionFactory;
    private Session session;
    private Transaction transaction;
    
    
    @Before
    public void before(){
        Configuration cfg=new Configuration().configure();
        ServiceRegistry serviceRegistry = new ServiceRegistryBuilder().applySettings(cfg.getProperties()).buildServiceRegistry();
        sessionFactory = cfg.buildSessionFactory(serviceRegistry);
        session=sessionFactory.openSession();
        transaction=session.beginTransaction();
   }
    @After
    public void after(){
	    transaction.commit();
	    session.close();
	    sessionFactory.close();
    }
	    
	    
    @Test
    public void testAdd(){
	    Students s=new Students("buer");
	    session.save(s);
    }
}
    
```

### 二、Hibernate与Spring协同工作

检查发现spring的配置文件`applicationContext.xml`中有了的数据库的配置，Hibernate的配置文件`hibernate.cfg.xml`中也配置了数据库信息。
其实`hibernate.cfg.xml`已经不再需要，可以将hibernate配置最为一个bean注入到spring中，为dataSource。再利用dataSource最为一个bean注入到sesssionFactory，并且可以将sessionFactory最为一个bean注入给transaction。

在spring的配置文件中可以指定hibernate的配置文件（在配置sessionFactory的时候）：
```xml
<bean id="sessionFactory" class="org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean">  
    <property name="configLocation">  
        <value>classpath:hibernate/hibernate.cfg.xml</value>  
    </property>  
    <property name="packagesToScan">  
        <value>com.tgb.entity</value>  
    </property>  
</bean>
```
也可以直接在spring的配置文件中写出来（分别配置datasource和sessionFactory）：
```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">  
    <property name="driverClassName" value="org.gjt.mm.mysql.Driver"></property>  
    <property name="password" value="1"></property>  
    <property name="url" value="jdbc:mysql://localhost:3306/ssh2"></property>  
    <property name="username" value="root"></property>  
</bean>  
<bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">  
	<property name="dataSource" ref="dataSource"></property>  
	<property name="hibernateProperties">  
		<props>  
			<prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>  
			<prop key="hibernate.hbm2ddl.auto">update</prop>  
			<prop key="hibernate.show_sql">true</prop>  
		</props>  
	</property>  
	<property name="mappingLocations">  
		<list>  
			<value>classpath:/com/tgb/entity/User.hbm.xml</value>             
		</list>  
	</property>  
</bean>   
```
在spring中一个重要的概念是事务管理器`transactionManager`，关于事务与事务管理器。