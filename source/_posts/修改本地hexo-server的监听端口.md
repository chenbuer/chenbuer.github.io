---
title: 修改本地hexo-server的监听端口
date: 2017-02-13 20:49:09
categories: 静态网页
tags: hexo
---

已经在本地模块中安装了hexo-server，而且也已经运行起来了：
```bash
czy@czy-PC MINGW64 /e/hexo (hexo)
$ hexo s
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```
但是打开浏览器*并不能正确的显示页面*，可能是在windows下端口冲突，故修改hexo-server的监听端口（文件路径为`node_modules/hexo-server/index.js`）：
<!--more-->
```javascript
hexo.config.server = assign({
  port: 4444,
  log: false,
  ip: '0.0.0.0',
  compress: false,
  header: true
}, hexo.config.server);
```
再次运行就可以正常显示了。
在知乎上有[这个问题的讨论](https://www.zhihu.com/question/28847824)
	  
