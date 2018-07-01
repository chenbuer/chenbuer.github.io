---
title: vue学习
date: 2018-07-01 17:20:52
categories: 前端
tags: 前端
---

### 一、处理cookie用模块js-cookie
```javascript
import Cookies from 'js-cookie'
// 获取到cookie中key对应的value值
return Cookies.get("key")
// 置cookie中key对应的值为value
return Cookies.set("key", "value")
```
<!--more-->
### 二、vuex使用
#### 2.1 vuex中getter
getter类似于vue组件中的computed计算属性
#### 2.2 故state中的值和getter中的值都可以直接当做属性使用（如state中有属性`count`,在getter中有属性`fullName`）
1. state中属性的使用方法：
在vue中注册了store之后，可以用如下两种方法获取到count值(在组件中放在computed中)：
- this.$store.state.count
- ...mapState(['count'])
2. state中属性的使用方法：
在vue中注册了store之后，可以用如下两种方法获取到fullName值(在组件中放在computed中)：
- this.$store.getters.fullName
- ...mapGetter(['fullName'])

#### 2.1 vuex中mapActions和mapperMutations
同样，vuex也为我们封装了action和mutation方法的简便使用：mapActions和mapperMutations。在组件methods中：
```javascript
methods:{
    ...mapActions(['actionFunc']),
    ...mapMutations(['mutationFunc'])
}
```

### 三、`!+`的使用
[参考链接](https://segmentfault.com/q/1010000011710961)

+是让字符串变成数值类型。
！是求反。

要看具体字段做理解。
在这里应该是判断某个值是否为0