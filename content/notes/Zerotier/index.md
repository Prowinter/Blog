+++
title =  "Zerotier 使用记录"
date = "2022-06-07"
weight = 10
categories = [
]
tags = [
  "NAT"
]

+++
利用Zerotier实现内网穿透,可实现任意地方访问局域网内设备。在Nat层允许的情况下采用P2P的方式,其他情况采用行星节点流量转发的方式。

<!--more-->
## 官方参考
* [Zerotier Document](https://docs.zerotier.com/zerotier/moons/)
* [Zerotier Download](https://www.zerotier.com/download/)

## 先验知识
* PLANET：行星服务器， Zerotier 各地的根服务器，有日本、新加坡等地。  
* MOON：卫星级服务器， 用户自建的私有根服务器，起到中转加速的作用。
* LEAF：相当于各个枝叶， 就是每台连接到该网络的机器节点。

## 各系统配置目录
- Windows: C:\ProgramData\ZeroTier\One
- Macintosh: /Library/Application Support/ZeroTier/One
- Linux: /var/lib/zerotier-one

## ZeroTier Moon私有中转节点配置
1. 生成moon模版
  ```shell 
  cd /var/lib/zerotier-one
  zerotier-idtool initmoon identity.public > moon.json
  ```
2. 修改moon.json配置
   
   `修改"stableEndpoints"为公网的 IP,例如"stableEndpoints": [ "8.8.8.8/9993" ]`
   
3. 生成签名文件,签名文件下发到各个枝叶,保存在配置文件下到moons.d文件夹目录下
   
  ```shell 
    zerotier-idtool genmoon moon.json
  ```
4. 重启各行星和卫星节点服务
  
  ```shell 
  Mac:
      sudo launchctl unload /Library/LaunchDaemons/com.zerotier.one.plist
      sudo launchctl load /Library/LaunchDaemons/com.zerotier.one.plist
  Windows:
      services.msc →  ZeroTier One → restart
  Linux:
      systemctl restart zerotier-one.service
  Openwrt:
      /etc/init.d/zerotier restart
  ```
### MOON节点表
  ![MOON](/images/Moon.png?width=600px#center)  

### LEAF节点表
  ![LEAF](/images/Leaf.png?width=600px#center)  

## 软路由以太网桥接
1. 软路由内开启Zerotier允许Zerotier接入客户端访问路由器Lan口资源

  ![application_1](/images/WAN_Bridge.png?width=600px#center)  

2. Zerotier内配置开启Allow Ethernet Bridging
   
  ![application_1](/images/Zerotier_Bridge.png?width=600px#center)  

3. Zerotier内添加路由表(确保网段和子网掩码与路由器内网配置一致)
   
  ![application_1](/images/Zerotier_Route.png?width=600px#center)  

4. 软路由内创建Zerotier网络接口,接口名类似于(ztyouvlahr),ipv4地址与ipv4子网掩码与Zerotier内保持一致。
  

5. 配置iptables/防火墙

  ```shell 
  iptables -I FORWARD -i ztyouvlahr -j ACCEPT
  iptables -I FORWARD -o ztyouvlahr -j ACCEPT
  iptables -t nat -I POSTROUTING -o ztyouvlahr -j MASQUERADE
  ```
