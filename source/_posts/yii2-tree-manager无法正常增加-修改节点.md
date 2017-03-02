---
title: yii2-tree-manager无法正常增加/修改节点
date: 2017-03-01 21:26:55
categories: PHP
tags: yii2-tree-manager
---

这个问题是因为treemanager使用的model是我用gii创造的，所以会有问题，可以参考[github上的讨论](https://github.com/kartik-v/yii2-tree-manager/issues/61)。
<!--more-->
将rules简单修改后如下：
```php
public function rules()
 {
return [
    // [['root', 'lft', 'rgt', 'lvl', 'icon_type', 'active', 'selected', 'disabled', 'readonly', 'visible', 'collapsed', 'movable_u', 'movable_d', 'movable_l', 'movable_r', 'removable', 'removable_all'], 'safe'],
    // [['lft', 'rgt', 'lvl', 'name'], 'safe'],
    [['content'], 'safe'],
    [['id'],'safe'],
    [['name'], 'string', 'max' => 60],
    [['icon'], 'string', 'max' => 255]
 ];
}
由于对于我来说，这个模型必须要在rules中的有id（唯一索引）、content（文章内容）、name（文章标题），这样才能正常进行增删查改。
```