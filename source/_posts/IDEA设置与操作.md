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

| 操作方法(Windows)    | 操作方法(Mac)     | 操作作用           |
| ------------------- |------------------|-----------------|
| ctrl+alt+o          |                  |删除不用的import   |
| ctrl+左击            |   command+左击  |   跳转到定义|
| shift+ctrl+左击      |                 |    跳转到接口实现|
| ctrl+F4              |                 |       关闭当前窗口|
| ctrl + alt + space   |                 |   智能提示|
| ctrl+j               |command + j      |    智能提示(一些简写的提示)|
| ctrl + U             |                 |      定位到类的父类、接口   |
| ctrl + H             | ctrl + H        |       查看类的继承结构      |
| ctrl + shift + H     |                 |        查看方法的继承结构      |
| ctrl + alt +H        |                 |            查看类或方法被调用情况  |
| alt + insert         |                 |        自动生成getter/setter |
| Ctrl + Shift + I     |                 |        原地参看类、方法的声明 |

[MAC下快捷键1](http://www.ituring.com.cn/article/37792)-[参考链接2](http://blog.csdn.net/wfp458113181wfp/article/details/24579781)-[MAC下快捷键2](http://wiki.jikexueyuan.com/project/intellij-idea-tutorial/keymap-mac-introduce.html)

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

### 八、删除文件，SVN提示Schedule for Deletion
> 现象：

![Schedule for Deletion](https://github.com/chenbuer/markdownImgs/blob/master/blog/scheduleForDel.png?raw=true)

> 不同选择会有不同的结果：选择Yes之后Status为deleted，选择No之后Status为missing

![不同选择会有不同的结果](https://github.com/chenbuer/markdownImgs/blob/master/blog/scheduleForDelAndChooseYes.png?raw=true)

### 九、IDEA的JDK设置

> 每个module都可以设置自己的：

![每个module单独设置](https://github.com/chenbuer/markdownImgs/blob/master/blog/IDEAAddJdk.png?raw=true)

> 全局的设置？如下：

![全局JDK设置](https://github.com/chenbuer/markdownImgs/blob/master/blog/globalJdkSetting.png?raw=true)

### 十、MAC下IDEA补全快捷键和搜狗快捷键冲突
这两个快捷键都是`ctrl+Space`，所以就给IDEA添加一个补全的快捷键为`Option+Command+Space`与Windows下的`Alt+Ctrl+Space`对应起来，但是`Option+Command+Space`又与系统快捷键冲突。处理方法如下：
> 删除系统的快捷键

![删除系统的快捷键](https://github.com/chenbuer/markdownImgs/blob/master/blog/idea-completion/rmMacAltCmdSpace.png?raw=true)
> 给IDEA增加一个快捷键
![给IDEA增加一个快捷键](https://github.com/chenbuer/markdownImgs/blob/master/blog/idea-completion/addOneCutForCompletion.png?raw=true)

### 十一、pom.xml中增加了jar包，但是在Library中没有添加进来

> 解决方法：

![pom.xml中增加了jar包，但是在Library中没有添加进来](https://github.com/chenbuer/markdownImgs/blob/master/blog/IDEAMvnAutoImport.png?raw=true)