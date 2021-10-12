---
title: RabbitMQ+MQTT插件-离线消息存储配置
date: 2021-09-013 13:16:07
tags:
  - RabbitMQ
  - MQTT
categories:
  - IOT
---

![壁纸](03.jpg)

实现功能，在消费者程序离线的场景下，生产者程序发布的消息，会保存到队列中。等消费者程序上线后获取消息。<br>



<!--more-->

#### 1 软件版本及环境说明

RabbitMQ(3.5.7) + MQTT 插件

【注】:

+ RabbitMQ (3.5.7) + MQTT插件: 离线消息存储功能, 消费者程序重复订阅，只会收到一次消息。

+ RabbitMQ (3.8.11) + MQTT插件: 离线消息存储功能, 消费者程序重复订阅，每次都会收到最近一次离线存储的消息。



[项目工程 GIT 地址](https://gitee.com/shadowbubble/rabbitmq-mqtt-plugin-in-test)

<br><br>

#### 2 核心配置参数

##### 2.1  消费者程序 (订阅动作)

+ **connect 环节**:

```shell
client_id = 唯一且同一个消费者程序 使用的 client_id 相同即可。

clean_session = False
```

+ **subscribe 环节**:

```shell
qos = 1
```

##### 2.2 生产者程序 (发布动作)

+ **publish 环节**: 

```shell
qos = 1

retain = True
```





<br><br>

#### 3 示例程序



##### 3.1 生产者程序

**mqtt_server_demo.py**

```python
import json

import time
import paho.mqtt.publish as publish

host = 'localhost'
port = 54881
auth = {
    'username': 'rabbit_user',
    'password': 'rabbit_passwd'
}

topic = '/cf/v1/AAA/012C/012C000003AX/set'
payload = f'message-{int(time.time())}'


def do_test():
    print(f'host: {host}, {port}')
    print(payload)
    publish.single(topic, payload=payload, port=port, hostname=host, auth=auth, retain=True, qos=1)


if __name__ == '__main__':
    do_test()
```



<br>

##### 3.2 消费者程序

**mqtt_client_demo.py**

```python
import paho.mqtt.client as mqtt


sub_qos = 1
sub_topic = '/cf/v1/AAA/012C/012C000003AX/set'

# The callback for when the client receives a CONNACK response from the server.
def on_connect(client, userdata, flags, rc):
    if rc:
        print("Connected with result code "+str(rc))

    # Subscribing in on_connect() means that if we lose the connection and
    # reconnect then subscriptions will be renewed

    client.subscribe(sub_topic, sub_qos)
    print(f'订阅主题: {sub_topic}, {sub_qos}')


# The callback for when a PUBLISH message is received from the server.
def on_message(client, userdata, msg):
    print(msg.topic+" "+str(msg.payload))

def do_test():
    client_kw = {
        'client_id': 'fixed_client_id',
        'clean_session': False,
        'userdata': None
    }

    client = mqtt.Client(**client_kw)
    client.on_connect = on_connect
    client.on_message = on_message

    host = 'localhost'
    client.username_pw_set('rabbit_user', 'rabbit_passwd')
    port = 54881

    print(f'host: {host}: {port}')
    client.connect(host, port, 60, )

    client.loop_forever()

if __name__ == '__main__':
    try:
        do_test()
    except KeyboardInterrupt:
        print('')
```



<br>

#### 4 NB 模块示例



##### 4.1 NB 模块通讯 AT 指令示例

AT 指令示例参数

```shell
AT+QMTCFG="version",0,4

AT+QMTCFG="session",0,0
AT+QMTOPEN=0,"ip",1883
AT+QMTCONN=0,"012C000003C7","012C&3356dd45d030abfec987748768c2&684177","5c1797541c9a08a6cbad1fc90f9a2"
AT+QMTPUB=0,1,1,1,"/cf/v1/AAA/012C/012C000003C7/post","{"EVT":0,"ST":[2,2,0,0,0],"BT":298,"AV":0,"HI":3600}"
AT+QMTSUB=0,1,"/cf/v1/AAA/012C/012C000003C7/set",1
```







