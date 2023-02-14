+++
title =  "Linux_SSH隧道"
date = "2022-02-07"
weight = 10
categories = [
]
tags = [
  "Linux",

  "Notes",

]

+++
利用SSH命令实现正反向代理。

<!--more-->

* 正向代理：将远端服务代理到本地端口。  
* 反向代理：将本地服务代理到远端端口。  

----------

* [SSH隧道技术\[饼哥学技术\]](https://www.bilibili.com/video/BV1F7411N7rQ)

----------

## 正向代理

> Specifies that connections to the given TCP port or Unix socket on the local (client) host are to be forwarded to the given host and port, or Unix socket, on the remote side.

```shell 
ssh -L [bind_address:]port:host:hostport
    -L [bind_address:]port:remote_socket
    -L local_socket:host:hostport
    -L local_socket:remote_socket
```


### 应用场景

![application_1](/images/Forward_1.png?width=900px#center)  
　　　  
## 反向代理
> Specifies that connections to the given TCP port or Unix socket on the remote (server) host are to be forwarded to the local side.
```shell
ssh -R [bind_address:]port:host:hostport
    -R [bind_address:]port:local_socket
    -R remote_socket:host:hostport
    -R remote_socket:local_socket
    -R [bind_address:]port
    -R [bind_address:]port:local_socket
    -R remote_socket:host:hostport
    -R remote_socket:local_socket
    -R [bind_address:]port
```
### 应用场景
![application_2](/images/Reverse_1.png?width=900px#center)
　　　  
### 应用场景

![application_3](/images/Reverse_2.png?width=900px#center)



## 拓展
`/etc/ssh/sshrc`
> Commands in this file are executed by ssh when the user logs in, just before the user's shell (or command) is started.  See the sshd(8) manual page for more information.

