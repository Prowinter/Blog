+++
title =  "蔬菜种植环境物联网监测系统"
date = "2022-03-02"
weight = 10
categories = [
]
tags = [
  "article",
]

+++
基于EMQx开源版的物联网监控系统，配合Flask实现数据持久化，集土壤温湿度，PH，空气温湿度，光照强度一体的监控系统，同时提供Linux,Windows上位机
<!--more-->

> [该项目仓库链接](https://github.com/Prowinter/IOT_Environmental_Monitoring)

> [EMQx](https://www.emqx.io/) An Open-Source, Cloud-Native, Distributed MQTT Broker for IoT

>[ESP8266 Web](https://github.com/tzapu/WiFiManager) Espressif ESPx WiFi Connection manager with fallback web configuration portal



## EMQx开源版

由于EMQx限制开源版使用插件[emqx_auth_mysql](https://www.emqx.io/docs/zh/v4.4/advanced/auth-mysql.html)可以使用[emqx_web_hook](https://www.emqx.io/docs/zh/v4.2/advanced/webhook.html)(网络钩子)配合flask将接收到的数据解析过滤后保存到Mysql数据库中




## emqx_web_hook配置

参考官方提供的文档，emqx_web_hook配置文件位于`etc/plugins/emqx_web_hook.conf`,Docker用户在容器内部对应位置。

api.url -> 事件需要转发的目的服务器地址

message.publish -> 服务端在发布（路由）消息前

------
## Flask

提供路由功能，可以利用路由功能监听WebHook服务的Post请求。

Python Mysql库：pymysql

`pip install pymysql`

flask简单例子:
```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
```


## Screen Tmux
由于Linux终端结束的同时会将运行的程序一同关闭，由此无法保证关闭终端后进行进行数据持久化。就需要使用终端复用器在后台运行相关的程序。Screen 和 Tmux 可以提供对应的功能。具体使用方式查询对应命令文档即可。

<br/>

## 数据处理
每天的数据可以通过Mysql内置的平均函数求平均值AVG()，每天的数据可以通过时间进行分组，通过Mysql上提前对数据进行预处理可以大大降低后续在QT上位机上的开发难度。

<br/>

```Mysql
#筛选三十天数据：
WHERE DATE_SUB(CURDATE(), INTERVAL 30 DAY) <= date(#时间字段)
#时间分组：
GROUP BY DATE_FORMAT(#时间字段,'%Y-%m-%d')
```

<br/>

## 数据虚构
仓库内同时提供前期数据量不足时的数据生成工具`Generate_Mysql_Rand_Date.py`。生成工具由python编写。

<br/>

## 拓展

>[IoTManager](https://iotmanager.ru/)

IoTManager支持Widget的方式构建，可以通过MQTT的方式传输指定的协议，构建自定义的程序页面。详情信息点击上文链接。APK程序存储在仓库DOC目录


![Center](/images/IoTmanager.PNG?width=350px&height=700px#center)

