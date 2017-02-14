---
title: 添加categories、tags和about
date: 2017-02-13 21:50:13
categories: 静态网页
tags: hexo
---
在直接利用hexo和freemind生成博客之后，点击categories、tags和about三个都是404的，

1. categories和tags的生成方法是（参考[categories和tags页面生成方法](https://github.com/wzpan/hexo-theme-freemind/issues/16)）：
```bash
1.manually create source/categories/index.html and paste the following contents：

title: Categories
layout: categories
---
2.manually create source/tags/index.html and paste the following contents：

title: Tags
layout: tags
---
3.restart hexo and refresh the browser
```

2. about页面有一个方法，应该也可以像上面一样手动创建(参考[about页面的生成方法](https://segmentfault.com/q/1010000000618915/a-1020000000752865)：
```bash
hexo new page "about"
```
当然，这个只是限于about页面的生成（参考[github中issue讨论](issue:https://github.com/wzpan/hexo-theme-freemind/issues/24)）

3. 若是想要categories页面也可以正常显示东西，需要在文章的开头书写格式为：
```bash
---
title: 添加categories、tags和about
date: 2017-02-13 21:50:13
categories: 静态网页
tags: hexo
---
```