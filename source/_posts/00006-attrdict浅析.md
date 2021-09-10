---
title: attrdict 模块浅析
date: 2020-03-04 00:27:09
tags:	
  - Python
  - 第三方库
categories:
  - Python
---



![](02.jpg)



<span style="margin-left: 32px;">attrdict 模块，允许创建属性风格访问的字典类, python中访问嵌套字典的键值属性，语法冗长, 借助这个库，可以实现类似 JavaScript 访问嵌套对象属性的效果。</span>

<span style="margin-left: 32px;">data["Schedule"]["events"]["speakers"][0]["name"] => data.Schedule.events[0].name</span>



<!--more-->



<br>

#### 1. 概述

##### 1.1 attrdict 模块

<span style="margin-left: 32px;">允许属性风格访问的字典类。</span>



##### 1.2 我为什么喜欢这个库？

<p style="margin-left: 20px;">设想存在以下场景，我们有一个 json 文件，需要读入到内存中，供程序访问。</p>

osconfeed.json

```json
{"Schedule": {
    
    "conferences": [{"serial": 115 }],
    "events": [
      {
        "serial": 34756,
        "name": "Graph Theory You Need to Know",
        "event_type": "40-minute conference session",

        "time_start": "2014-07-22 14:30:00",
        "time_stop": "2014-07-22 15:10:00",
        "venue_serial": 1452,
        "description": "A brief and friendly tour of the basics of graph theory, including a description and classification of the kinds of graphs and some interesting problems they can be employed to solve.",
        "website_url": "http://oscon.com/oscon2014/public/schedule/detail/34756",
        "speakers": [137697],
        "categories": [
          "Computational Thinking"
        ]
      },
      {
        "serial": 34757,
        "name": "The Data Structures (You Think) You Need to Know",
        "event_type": "40-minute conference session",

        "time_start": "2014-07-23 13:40:00",
        "time_stop": "2014-07-23 14:20:00",
        "venue_serial": 1448,
        "description": "A fun and approachable tour of some otherwise intimidating data structures. Learn how to solve difficult problems efficiently through the clever organization and linking of data.",
        "website_url": "http://oscon.com/oscon2014/public/schedule/detail/34757",
        "speakers": [137697],
        "categories": [
          "Computational Thinking"
        ]
      }
  ]
}}
```



**我们可以如下访问编码：**

```python
import json


def load_data(filepath):
    with open(filepath, 'r') as fp:
        return json.dumps(fp.read())


def do_something():
    data = load_data('./osconfeed.json')
    print(data["Schedule"]["events"]["serial"])						
    print(data["Schedule"]["events"]["speakers"][0]["serial"])
    print(data["Schedule"]["events"]["speakers"][0]["name"])
    # do something...
```

<p><span style="margin-left: 32px;">通过 json库，</span>我们可以很容易把 json 文件的内容，转换为 Python 的 dict 对象，进而通过 dict 来访问数据。不过 data["Schedule"]["events"]["speakers"][0]["name"] 这种语法冗长。在 JavaScript 中，可以使用 data.Schedule.events[0].name  来查询这个值。 幸运的是，通过 attrdict 模块，就可以很容易的做到。</p>



```python
import json
from attrdict import AttrDict

def load_data(filepath):
    with open(filepath, 'r') as fp:
    	return AttrDict(json.dumps(fp.read()))

    
def do_something():
    data = load_data('./osconfeed.json')
    print(data.Schedule.events.serial)						
    print(data.Schedule.events.speakers[0].serial)
    print(data.Schedule.events.speakers[0].name)
    # do something...
```

<br>



#### 2. 原理浅析

Python 访问对象的属性



```
Python中访问对象的属性，使用了 “.” 操作符。 

obj.attr  	=>	getattr(obj, attr)  		=> 	__getattr__(self, obj, attr)				
obj.attr=val	=> 	setattr(obj, attr, val) 	=> 	__setattr__(self, obj, attr, val)		
```

<span style="margin-left: 32px;">故借助一个代理类，</span>重新实现上述两个方法，可以以访问属性的方式，来访问 dict 对象的键值属性。





<br>

#### 3. 源码浅析







<br>

#### 4. 拓展阅读

**《流畅的Python》** 第19章 动态属性和特性，实现了 FrozenJSON 类，比大多数实现都简单。这个类能递归自动处理嵌套的映射和列表。



<br>

<br>





































































