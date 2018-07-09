---
title: buer系统过程中的问题
date: 2018-07-08 15:03:52
categories: java
tags: java
---

### 一、controller中收不到post发来参数
[参考一](https://blog.csdn.net/qq_35420123/article/details/80674861)

[参考二](https://github.com/axios/axios/blob/master/README.md#using-applicationx-www-form-urlencoded-format)
<!--more-->
解决方法是用qs将参数进行stringify

### 二、vue-router中动态添加路由
用addRoutes方法，调用该方法前后打印router.options.routes是没有用的，都没有变化，但是动态路由其实已经设置进去了。 

另外显示的地方需要在Sidebar/index.vue下，将sidebar的route属性改成想要的值，也就是store中permission中的routers属性。