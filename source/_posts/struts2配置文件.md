---
title: Struts2配置文件
date: 2017-03-01 21:20:01
categories: JAVA
tags: struts
---

#### 一、web.xml

路径为webRoot/WEB-INF：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
  <display-name>hw</display-name>
  <filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```
<!--more-->
#### 二、struts.xml
位于src目录下：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
<!-- 包含多个配置文件 -->
    <include file="file1.xml"></include>


    <package name="default" namespace="/" extends="struts-default">
    <!-- 这个必须放到第一行 -->
        <default-action-ref name="error"></default-action-ref>
           <action name="helloworld" class="info.baitian.action.HelloWorld">
               <result>/result.jsp</result>
           </action>
           
           <action name="love" method="love" class="info.baitian.action.HelloWorld">
               <result name="error">/love.jsp</result>
           </action>
           
           <action name="error">
               <result>/error.jsp</result>
           </action>
   </package>
</struts>
```