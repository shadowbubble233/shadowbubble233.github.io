---
title: attrdict 模块浅析
date: 2021-09-06 00:27:09
tags:	
  - Python
  - attrdict
  - 第三方库
---



![](01.jpg)

<p>attrdict 功能简介</p>



<p>我为什么喜欢这个框架</p>

<!--more-->



#### 1. attrdict 模块概述

允许属性风格访问的字典类。

AttrDict 库允许，允许将他们的元素按属性的风格访问。



属性访问让它很容易的创建便利的，层次化的设置对象。

with open('settings.yaml') as fileobj:

​	settings = AttrDict(yaml.safe_load(fileobj))



cursor = connect(**settings.db.credentials).cursor()

cursor.execute('select column from table;')



基本使用：

AttrMap

AttrDict

AttrDefault



允许创建，访问，删除属性。



valid Names















#### 2. 原理浅析



#### 3. 源码浅析











































































