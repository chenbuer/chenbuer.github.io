---
title: springMVC中利用@service和@Resource注释
date: 2017-04-01 00:52:41
categories: JAVA
tags: spring
---

> 在service层定义一个service类，用注释@Service；在利用这个bean的时候用@Resource表示：

举例如下：
<!--more-->
### 一、在service层定义这个bean
```java
package com.chenbuer.service.impl;

import org.springframework.stereotype.Service;

import com.chenbuer.service.UserService;

/**
 * UserService的实现类
 */
@Service("userService")
public class UserServiceImpl implements UserService {

}

```

### 二、在controller层使用这个bean
```java
package com.chenbuer.controller;

import javax.annotation.Resource;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.chenbuer.service.UserService;

/**
 * 用户controller层
 */
@Controller
@RequestMapping("/user")
public class UserController {

	@Resource
	private UserService userService;
}

```