---
title: buer系统部署
date: 2018-07-28 00:24:52
categories: java
tags: java
---

### 一、Nginx虚拟机
不再赘述，碰到一个坑就是：虚拟机配置好了，但是admin的url一直访问不了，原来是因为自己没有配置DNS的A类型域名解析。
<!--more-->
### 二、安装redis
直接利用的yum工具安装

[参考链接](https://www.jianshu.com/p/ebda253a8daa)

### 三、安装数据库mariadb
直接利用yum工具安装：`yum install mariadb-server mariadb `

[参考链接](http://www.cnblogs.com/starof/p/4680083.html)

```shell
systemctl start mariadb  #启动MariaDB
systemctl stop mariadb  #停止MariaDB
systemctl restart mariadb  #重启MariaDB
systemctl enable mariadb  #设置开机启动
```
直接登录，没有密码:`mysql -u root -p`

修改密码:
```shell
1. 更新 mysql 库中 user 表的字段：
MariaDB [(none)]> use mysql;  
MariaDB [mysql]> UPDATE user SET password=password('newpassword') WHERE user='root';  
MariaDB [mysql]> flush privileges;  
MariaDB [mysql]> exit;
 
2. 或者，使用 set 指令设置root密码：
MariaDB [(none)]> SET password for 'root'@'localhost'=password('newpassword');  
MariaDB [(none)]> exit; 
```
[参考链接](https://blog.csdn.net/asdfg6541/article/details/78866421)


### 四、springboot打包之后java -jar显示：没有主清单属性
不应该的，原来都是OK的，想来可能是因为自己的项目把parent改成了自己的。解决方法，将plugin为`spring-boot-maven-plugin`修改添加：
```xml
<executions>
    <execution>
        <goals>
            <goal>repackage</goal>
        </goals>
    </execution>
</executions>
```
[参考](http://www.cnblogs.com/niceboat/p/6230448.html)

### 五、启动时报错:Error creating bean with name 'freeMarkerConfigurer'
看上去报错freemarker的问题，估计是我项目中自己引用了freemaker的解析包，用来自动生成代码的，冲突有问题，所以，就想在springboot中不用freemarker，毕竟我是前段后分离，[解决方法参考](https://fastfoodcoding.com/questions/1506440393799/how-to-disable-freemarker-templates-in-spring-boot):

applicaiton.properties中添加：
```xml
spring.freemarker.check-template-location=false
```
APP上添加注解：
```java
@EnableAutoConfiguration(exclude = { FreeMarkerAutoConfiguration.class })
```

### 六、命令行执行sql文件
[参考链接](https://www.jianshu.com/p/43052c237bf1)
```shell
mysql -u root -p
use xxx;
source path/xxx.sql
```

### 七、打开8080端口
[参考文档](https://www.jianshu.com/p/b66277ad24c2)

除了要在安全策略组里面打开8080，还需要设置防火墙，我是直接关闭`systemctl stop firewalld.service`

**attention: 后来改成了利用Nginx的反向代理，就不需要打开8080端口了**

### 八、Nginx部署vue项目，刷新之后是Nginx的404页面
在server的配置中加上如下：
```shell
location / {
    try_files $uri $uri/ /index.html last;
    index index.html;
}
```
[参考链接](https://blog.csdn.net/zjcjava/article/details/78255018)

attention：对于java后台数据的请求利用Nginx的反向代理实现：
```shell
location /api {
    proxy_pass http://127.0.0.1:8080/api/;
}
```

### 九、springboot运行一段时间之后就挂了
[参考链接](https://blog.csdn.net/zzq900503/article/details/80577490)

个人估计远程的bash断了之后，对应的进行也就断了，所以用nohup命令，[参见](https://www.cnblogs.com/superjt/p/4079013.html)。`nohup java -jar buer-***.jar`

### 十、springboot和vue整合
可以Google下，还没细看，[参考链接一](https://blog.csdn.net/u014098584/article/details/78912378)

[参考链接二](https://blog.csdn.net/sinat_27639721/article/details/78229040)

### 十一、关于加载慢和动态加载的问题
Google下`vue 动态加载 resolve`