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
<!--more-->
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

### 五、ThreadPoolTaskExecutor的使用
关于它的配置可以[参考链接](http://blog.csdn.net/seminmredoxu/article/details/7000709)
使用方法：
首先定义线程池：
```xml
<bean id ="taskExecutor"  class ="org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor" >
    <property name ="corePoolSize" value ="5" /> 
    <property name ="keepAliveSeconds" value ="300" /> 
    <property name ="maxPoolSize" value ="10" /> 
    <property name ="queueCapacity" value ="25" /> 
  </bean>
```
然后使用线程池：
```java
public class ThreadPoolTaskExectorTest {
    /**
     * 线程池
     */
    @Autowired
    @Qualifier("runBatchTaskExecutor")
    ThreadPoolTaskExecutor taskExecutor;

    @Test
    public void crateTask(){
        CreateLoanInfoTask task = new CreateLoanInfoTask();
        taskExecutor.execute(task);
    }
    
    class CreateLoanInfoTask implements Runnable{

        public void run() {
            // 省略具体的逻辑
        }
    }
}
```
只要把要执行的runnable对象（task）扔进`ThreadPoolTaskExecutor taskExecutor`中去，然后他就自己按照配置策略，去执行对应的runnable对象中的run方法。

另外：
[Callable和Runnable用法的比较](http://blog.csdn.net/xtwolf008/article/details/7713580)

### 六、Callable、Future和FutureTask
菜鸟A：问一个基础的问题，线程池可以理解为java异步编程的一种方法吗？

老鸟B：对啊，多线程就是一种异步编程啊。多线程就是为了达到异步的

菜鸟A：在上面讲的ThreadPoolTaskExector。如果线程池连的Runnable里面的run方法，是跟另一个系统交互的。另一个系统返回的response怎么处理呢？

他可以在run()中等待结果response，可以立即处理response，也可以将response封装成一个runnable对象再次扔到其他的线程池中等待处理。

但是，他还是需要等待response的。

老鸟B：这个时候用Callable、Future和FutureTask就更好了

[Callable、Future和FutureTask](http://www.cnblogs.com/dolphin0520/p/3949310.html)

### 七、FTP操作
利用commons-net这个jar包：
```xml
<dependency>
    <groupId>commons-net</groupId>
    <artifactId>commons-net</artifactId>
    <version>3.3</version>
</dependency>
```