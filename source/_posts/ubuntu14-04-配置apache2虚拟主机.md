---
title: ubuntu14.04 配置apache2虚拟主机
date: 2017-03-01 21:39:29
categories: Linux
tags: apache2
---
在很多的教程中告诉我们设置apache的虚拟主机都是配置apache配置文件，但是在ubuntu14.04中按照一些教程配置，并不一定能够成功。下面简单介绍一下在ubuntu14.04下配置apache的虚拟主机。

- 到/etc/apache2/sites-available/拷贝一份原来的default的配置文件：
`cp default-ssl.conf www.local.conf`
- 配置一下新的虚拟主机www的配置文件，由于较多，就附在最后面
- 启动这个虚拟主机。其实就是在/etc/apache2/sites-enabled中添加一个www的虚拟主机的软连接：
`sudo a2ensite www.local.conf`
- 开启复写。【需要注意的是，开启复写不仅仅是上面一行代码那么简单，需要在www虚拟主机的配置文件中加上部分复写的配置，可以参考下面的www虚拟主机的配置文件】
- 重启apache服务。
`sudo service apache2 restart`
<!--more-->
----

下面是www虚拟主机的配置文件：
```xml
<VirtualHost *:80>
 # The ServerName directive sets the request scheme, hostname and port that
 # the server uses to identify itself. This is used when creating
 # redirection URLs. In the context of virtual hosts, the ServerName
 # specifies what hostname must appear in the request's Host: header to
 # match this virtual host. For the default virtual host (this file) this
 # value is not decisive as it is used as a last resort host regardless.
 # However, you must set it for any further virtual host explicitly.
 #ServerName www.example.com

 ServerAdmin webmaster@localhost
 ServerName www.baitian.info
 ServerAlias www.baitian.info
 DocumentRoot /path/to/your/wwwRoot

 # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
 # error, crit, alert, emerg.
 # It is also possible to configure the loglevel for particular
 # modules, e.g.
 #LogLevel info ssl:warn

 ErrorLog ${APACHE_LOG_DIR}/error.log
 CustomLog ${APACHE_LOG_DIR}/access.log combined
 <Directory /path/to/your/wwwRoot>
 
 Options Indexes FollowSymLinks
 
 AllowOverride ALL
 
 Require all granted
 
 </Directory>

 # For most configuration files from conf-available/, which are
 # enabled or disabled at a global level, it is possible to
 # include a line for only one particular virtual host. For example the
 # following line enables the CGI configuration for this host only
 # after it has been globally disabled with "a2disconf".
 #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```