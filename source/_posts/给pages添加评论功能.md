---
title: 给pages添加评论功能
date: 2017-11-19 05:11:00
categories: 静态网页
tags: hexo
---

> 最近很多的评论插件都停用了，多说直接停止服务，disqus经常访问不了。所以只能求助GitHub，给了我们新的利器[Gitment](https://github.com/imsun/gitment)

具体的使用步骤如下：

<!--more-->
#### 一、首先需要在GitHub的OAuth Apps里面注册一个repo
这个可以参考gitment的文档，不赘述，也是GitHub OAuth的一般注册方法

#### 二、将Gitment的静态文件配置到文章中
我的文件主要是配置在themes->freemind->layout->_partial->post->comment.ejs文件中。

【完毕】