---
title: Hibernate的操作流程
date: 2017-02-11 01:55:52
categories: java
tags: Hibernate
---

### 在一个测试类中表示如下所示：

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
