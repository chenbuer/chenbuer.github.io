---
title: jsp添加jstl库
date: 2017-04-06 00:27:04
categories: JAVA
tags: spring
---
一、在pom.xml中添加依赖：
```xml
<!-- 添加jtl支持 -->
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>jstl</artifactId>
		<version>1.2</version>
	</dependency>
```
<!--more-->
二、在需要利用的地方需要加上标签
<%@ taglib prefix="c" 
           uri="http://java.sun.com/jsp/jstl/core" %>
三、具体利用方法可以参考[jsp常用用法](http://www.runoob.com/jsp/jsp-jstl.html)