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
- 行内元素(内联元素)，一行显示，直到这行显示满了会换行
***行内元素并不具有宽高设置，且垂直方向上margin和padding都是不生效的***

\<span\> \<a\> \<em\>(变斜) 等

- 块级元素，独占一行

\<p\> \<div\> \<h1-6\> \<ul\> \<li\> \<ol\> \<dl\> \<dt\> \<dd\> 等

- dispaly:inline

    元素显示为内联元素，元素前后没有换行符

- display:block

    元素显示为块级元素，前后有换行符

- display:inline-block

    行内块元素，元素呈现为inline，具有block相应特性(width、height、padding、margin都生效)


# 背景样式
- background-color 设置元素的背景颜色
- background-img   把图像设置为背景
- background-position   设置背景图像的起始位置
- background-attachment 背景图像是否固定(固定住用fixed，sroll)
- background-repeat     背景图像是否重复&如何重复(no-repeat,repeat-x,repeat-y,repeat[默认])

# 盒子模型
- 内边距 padding
    - padding:值1 // 4个方向都为值1
    - padding:值1 值2   // 值1是上下，值2是左右
    - padding:值1 值2 值3   // 值1是上，值2是左右，值3是下
    - padding:值1 值2 值3 值4 //分别对应上、右、下、左
- 外边距 margin
    - margin可以设置为负值，但是padding不可以设置为负值
- 边框 border
    - border-width
    - border-color
    - border-style(none,dotted,dashed,solid,groove[3D凹槽边框]...)

# float属性

一旦给元素设置了浮动属性，元素就脱离原来的文档流；但是会正常占用文字的空间（不会覆盖文字）
- float:left
- float:right
- float:none
- float:inherit

# clear属性：消除浮动带来的副作用
- clear:none
- clear:left
    - 不允许该元素(标签)左边有浮动元素
- clear:right
    - 不允许右边有浮动元素
- clear:both
    - 左右都不允许

# 三中布局方式
- 标准流
- 浮动
- 定位 (position)
    - static(默认)
    - relative,用top、bottom、left、right来配合使用，发生位移
    - absolute,用top、bottom、left、right来配合使用，与relative相比较，位移的方向会变化，同时，脱离正常的文档流
    - fixed：用top、bottom、left、right来配合使用，位置不变，脱离文档流