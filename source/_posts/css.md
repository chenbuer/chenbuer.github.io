---
title: css
date: 2018-06-10 15:03:52
categories: 前端
tags: 前端
---

# 伪类，不一定只对a标签，其他标签也适用
- :link   a标签未访问状态
- :visited   已访问状态
- :hover    鼠标悬浮状态
- :active   激活状态，比如点击了
<!--more-->

# 样式继承
父元素的样式，子元素可以从父元素那儿继承部分样式。如border就不可以继承

# 后代选择器
如 `p a em` 就表示p标签中a标签中的em标签


# 文字样式设计属性
- font-family   字体
- font-size     文字大小
    - 绝对单位
        - in 英寸
        - cm 厘米
        - mm 毫米
        - pt 磅
        - pc pica，1pc=12pt
        - xx-small 9px
        - x-small
        - small
        - medium 16px
        - large
        - x-large
        - xx-large 28px
    - 相对单位
        - px 像素，受分辨率的影响
        - larger（相对父元素大点儿） smaller（相对父元素小点儿）
        - **em** 相对于父元素字体大小的n倍
        - percent 相对于父元素字体大小的具体倍数
- font-color    文字颜色
- font-weight   文字粗细
- font-style    文字样式

# 行内元素 & 块元素
行内元素 // todo

# 背景样式
- background-color 设置元素的背景颜色
- background-img   把图像设置为背景
- background-position   设置背景图像的起始位置
- background-attachment 背景图像是否固定(固定住用fixed，sroll)
- background-repeat     背景图像是否重复&如何重复(no-repeat,repeat-x,repeat-y,repeat[默认])

# 盒子模型
- 内边距 padding
- 外边距 margin
- 边框 border
    - border-width
    - border-color
    - border-style

