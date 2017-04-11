---
title: tomcat应用技巧
date: 2017-04-09 17:44:20
catogories: JAVA
tags: tomcat
---
这篇文章介绍了：
- URL中去掉项目名
- URL中携带中文避免乱码

### 一、URL中去掉项目名
部署一个项目到tomcat中的，在server.xml中会增加一行记录，如：
```xml
      <Context docBase="blog" path="/blog" reloadable="true" source="org.eclipse.jst.j2ee.server:blog"/></Host>
```
然后我们本地访问这个项目的时候需要输入的URL如下：
<!--more-->
> http://localhost:8080/blog
但是我们实际部署到的时候，一般是这样的：
> http://www.chenbuer.com/
只有域名，而没有多余的路径（也就是上面的项目名）。那么在编码的时候如何去掉这样的项目名，使得编码的时候与实际部署的时候没有区别，操作如下：
将server.xml中的Content标签改为如下（**去掉的path中的值**）:
```xml
      <Context docBase="blog" path="/" reloadable="true" source="org.eclipse.jst.j2ee.server:blog"/></Host>
```
### 二、URL中携带中文避免乱码
在server.xml中的Connector中添加**URIEncoding="UTF-8"**(一定要注意不是**URLIEncoding="UTF-8"**)，变为如下:
```xml
<Connector URIEncoding="UTF-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```