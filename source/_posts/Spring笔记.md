---
title: Spring笔记
date: 2017-12-28 17:00:00
categories: JAVA
tags: JAVA
---

### 一、Ajax请求返回json对象
直接用`@Response`会出现中文乱码的情况，所以之前做的时候都写了一个工具类：
[ResponseUtil.java](https://github.com/chenbuer/springboot-soa-blog/blob/master/springboot-soa-blog-controller%2Fsrc%2Fmain%2Fjava%2Fcom%2Fchenbuer%2Futil%2FResponseUtil.java)
<!--more-->
其实可以这样写
```java

@RequestMapping(value = "/path/of/URL", produces = { "application/json;charset=UTF-8" })
@ResponseBody
public String getQszgListInfo(HttpServletRequest request) {

        // 要返回的json
        Map<String, Object> res = new HashMap<String, Object>();
        ...
        return JSONObject.toJSONString(res);
    }
```
返回文本，RequestMapping可以写为`@RequestMapping(value = "/path/of/URL", produces = "text/plain;charset=UTF-8")`。[官方文档](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html#produces--)

### 二、tiny-spring学习
1. step-1-container-register-and-get

`BeanFactory`是注册和获取bean的工厂，说白了就是讲bean初始化并放到一个Map中，key值就是beanName，获取的时候，根据这个beanName获取到他的对象。就是这么简单。
