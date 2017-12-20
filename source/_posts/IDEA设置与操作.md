---
title: IDEA设置与操作
date: 2017-12-11 20:29:20
categories: JAVA
tags: 工具
---
### 一、工具栏中文乱码，但是编辑框不乱码

进入IDE Settings 里的 Appearance项，选中Override default fonts by ，把 Name 设置为 SimSun，Size 根据自己喜好设置（我一般设为 12）。确定
<!--more-->
[参考链接](http://www.cnblogs.com/jlxuqiang/p/4083596.html)
### 二、alt+ctrl+箭头 与系统热键冲突
在IDEA中alt+ctrl+←代表后退，
alt+ctrl+→代表前进，但是Windows本身有热键，表示旋转屏幕，那么就去除旋转屏幕的热键。

[参考链接](https://jingyan.baidu.com/article/ceb9fb10dfc7c28cad2ba026.html)

### 三、键盘映射

| 操作方法             | 操作作用           |
| ------------------- |-----------------|
| ctrl+alt+o          |删除不用的import   |
| ctrl+左击            |   跳转到定义|
| shift+ctrl+左击      |    跳转到接口实现|
| ctrl+F4              |       关闭当前窗口|
| ctrl + alt + space   |   智能提示|
| ctrl+j(command + j)  |    智能提示(一些简写的提示)|
| ctrl + U             |      定位到类的父类、接口   |
| ctrl + H             |       查看类的继承结构      |
| ctrl + shift + H     |        查看方法的继承结构      |
| ctrl + alt +H        |            查看类或方法被调用情况  |
| Ctrl + Shift + I     |        原地参看类、方法的声明 |

[参考链接1](http://www.ituring.com.cn/article/37792)-[参考链接2](http://blog.csdn.net/wfp458113181wfp/article/details/24579781)

### 四、不能创建package
![不能创建package](https://github.com/chenbuer/markdownImgs/blob/master/blog/canNotCreatePackage.png?raw=true)

### 五、使得package展示和eclipse一样
![使得package展示和eclipse一样](https://github.com/chenbuer/markdownImgs/blob/master/blog/makePackageLikeEclipse.png?raw=true)

### 六、函数的注释因为跟入参不一致，IDEA红色报错
![函数的注释因为跟入参不一致，IDEA红色报错](https://github.com/chenbuer/markdownImgs/blob/master/blog/annotationIsRed.png?raw=true)

[参考链接](http://blog.csdn.net/river_continent/article/details/72841428)

### 七、IDEA中SVN不好使
> 现象：

![IDEA中SVN不好使](https://github.com/chenbuer/markdownImgs/blob/master/blog/ideaSvnUnuseful.png?raw=true)

> 解决方法：

![IDEA中SVN不好使的解决方法](https://github.com/chenbuer/markdownImgs/blob/master/blog/ideaSvnUnusefulSolution.png?raw=true)

[参考链接](http://blog.csdn.net/u012940983/article/details/38679667)