---
title: Java基础
date: 2017-12-25 14:03:20
categories: JAVA
tags: JAVA
---
### 一、List处理

> 1.1 List截取字符串

```java
noRaiseDrop.subList(fromIndex, toIndex);
```

> 1.2 List拼接

用Apache的org.apache.commons.lang.StringUtils.
```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.4.2</version>
</dependency>
```
```java
StringUtils.join(list, ",")
```

### 二、Spring + tomcat jndi配置
[配置方法](http://www.cnblogs.com/zhilin-yang/p/4958000.html)

### 三、org.springframework.beans.BeanUtils#copyProperties的使用
```java
public class BeanUtilsCopyTest {
    @Test
    public void test(){
        A a = new A("a",1,2L,"d");
        System.out.println("a======");
        System.out.println(a);
        B b = new B();
        System.out.println("b======");
        BeanUtils.copyProperties(a,b);
        System.out.println(b);
    }

    class A{
        private String a;
        private int b;
        private long c;
        private String d;
        // 省略getter/setter和toString()和构造函数
    }


    class B{
        private int b;
        private long c;
        private String d;
        private String e;
        // 省略getter/setter和toString()
    }
}
```
运行结果
```java
a======
A{a='a', b=1, c=2, d='d'}
b======
B{b=1, c=2, d='d', e='null'}
```

### 四、@Qualifier与@Autowired
简单讲，就是@Autowired是根据类型来寻找bean的，当一个接口可能有好几个实现类，并且都注释为Bean，就需要@Qualifier来指定对应的bean id。
[参考链接](http://blog.csdn.net/clerk0324/article/details/25198457)