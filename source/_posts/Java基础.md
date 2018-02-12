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