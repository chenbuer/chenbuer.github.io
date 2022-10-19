---
title: 关于我
date: 2017-02-13 21:27:44
---

> ### 我是陈不二，请多指教 Here is my schedule:

个人规划先学习好一个完整的框架，所以利用[zheng项目](https://github.com/chenbuer/zheng)，不过这个项目有很多未完善的地方，issue和群都几乎没什么回答，no choice，自己去挖掘把。

---

 下面主要学习dubbo，任重道远。(dubbo源码学习还是需要具备一定的基础，自己已经学习完了tinySpring和dom4j，但是SPI等基础还是需要深入学习)
 学习dubbo需要先了解spring，看了tiny-spring，还需要了解一下xml解析，用原生的dom包不如学习一下dom4j。

如何提高自己的情商，需要长久锻炼与思考。

很多时候自己心生不满就是自己没有理解 理想与现实 之间的区别。生活本身就不是自己所想象的那样。

---
这两天看的东西有点杂：druid、shiro、maven、hibernate和springMVC结合。
看hibernate和spingmvc主要是因为学习maven的时候，配置看的有点不清晰，当时自己做小项目的时候也只是出了问题重新配，能跑就行。
所以有必要自己规划一下学习的路线。
实话说，自己一个人从主配置到把项目跑起来，期间的累只有自己知道。毕竟：
> #### 经历了千辛万苦获得到的东西，在最后才太会笑得很开心。


只是学习SSM，已经不能满足新的技术需求了，为了更上一层楼，给自己分配一些新的任务：
- 用dubbo将服务进行拆分，拆分成两个部分，一个是视图层和控制层，另一个是结合了dao层的持久层。
(今天[20170618]终于将第一部分完成了，在启动服务的时候，有很多jar不符合，当时的做法是将原来的单层结构的pom.xml直接整到了facade里面，而controller和service都引用；在启动controller的时候，出现了两个问题；[1]监听器问题：InitComponent类implements AplicationContextAware，实现了setApplication方法了，应该不会出现applicationContext为空的情况，但是debug可见该值为空，查找原因是applicaitonContext.xml中没有配置这个bean，[查看教程](http://www.cnblogs.com/kxdblog/p/5988027.html), [2]就是困扰了一天的（java.lang.NoSuchMethodError: org.springframework.util.ClassUtils.getMethod(Ljava/lang/Class;Ljava/lang/String;[Ljava/lang/Class;)Ljava/lang/reflect/Method;）错误，个人认为是dubbo引用的比较旧的spring，经询问，解决方法是在dubbo的dependecy中配置extension标签规避，查看[网上人家dubbo结合springMVC项目](http://blog.csdn.net/aixiaoyang168/article/details/51362675))
- 将结合了持久层改成用springboot框架处理。
- 利用vue将试图层和控制层解耦，这样就完全将MVC进行了解耦。
- 利用docker将不同的服务封装到不同的容器中。当然了，数据库mysql一开始就可以设置为docker容器。


- - - 
东西看的太乱也有糊涂的时候：在看[shiro](https://github.com/apache/shiro)([教程](http://jinnianshilongnian.iteye.com/blog/2018398))的时候去[druid](https://github.com/alibaba/druid)([教程](http://blog.csdn.net/yunnysunny/article/details/8657095))源码里面搜SecurityUtills类，没找到，怀疑自己是不是进了一个假的repo。所以一个scheme的需求迫在眉睫。

Scheme：
- hibernate与springMVC结合配置 
- hibernate和springMVC在使用maven情况下的配置与使用 
- druid与hibernate在结合了spring的情况下的使用  
- shiro的使用
- 利用的easyui完成一个后台的通用管理系统，当然也可以自己找一个repo去fork看懂，甚至pullRequest
- 看一个基于bootstrap的后台管理系统，这个在做完上一个任务的情况下应该轻松一点(这个暂时不做了，还是先做好一个吧)
- 分布式学习dubbo zookeeper
- no sql的几个框架redis mangodb
- 消息队列kafka和各种mq
