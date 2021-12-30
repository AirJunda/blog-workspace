---
title: "Docker: Network"
date: 2021-12-18T22:39:12+08:00
draft: false
tags: ["Docker","Tech", "Container"]
topics: ["TechBlog"]
---

## Docker笔记之网络相关
这篇是Docker从入门到精通课程的网络章节的复习。docker的网络相关内容主要涉及多容器间的通信，容器端口转发，暴露等内容。

&nbsp;

## 网络基础回顾
看这篇文章去复习 [Traffic example, the full picture](https://www.homenethowto.com/advanced-topics/traffic-example-the-full-picture/)

&nbsp;
## 网络相关linux command
* ifconfig
* ping
* telnet
* tracepath
* curl      
以下我觉得可以补充了解的命令
    * netstat

## Udemy课上Demo例子
使用Busybox image。Busybox是一个集成了一百多个最常用Linux命令和工具的软件工具箱，它在单一的可执行文件中提供了精简的Unix工具集;   别的很多image你进去连ifconfig命令都没装        
Demo用到的命令如下：
```
#启动容器命名为box1
docker container run -d --rm --name box1 busybox /bin/sh -c "while true; do sleep 360; done"
#进入容器
docker container exec -it box1 sh
#进入容器后看本容器的ip address
ip a
# 不进入容器看ip address
docker container inspect box1
# VM中访问某个容器的80端口
curl xxx.xxx.xx.xx:80
# 下载nginx80端口的默认页面
wget xxx.xxx.xxx.xxx:80
```
这个例子就是演示了下容器，host, 不同host的容器之间的一些Ping,curl操作的结果。没啥教学内容。主要是看看几个docker用到的命令而已。知识类的讲解看下面

## Docker 网络之Bridge模式
默认下，一台VM中的多个docker可以互相通信。VM可以ping通本VM上运行的容器。但是另一台VM2无法ping通VM1上的docker address；

一台host上的容器和VM构成了一个bridge网络。VM当上了交换机（gateway）的作用。容器连接在这网络上。就好像这个局域网里的不同设备。默认下，创建的容器都会和gateway连接。这个Bridge网络默认的名字叫docker0

查看docker的网络的命令有：
```
# 看当前dokcer引擎建立的网络
docker network ls
# 如果有容器创建。会有个bridge网络。查看该网络中容器的ip, gateway和其他信息
# docker network inspect bridge
```

可以apt 安装一个叫brctl的tool


    ``brctl`` 使用前需要安装, 对于CentOS, 可以通过 ``sudo yum install -y bridge-utils`` 安装. 对于Ubuntu, 可以通过 ``sudo apt-get install -y bridge-utils``


    $ brctl show
    bridge name     bridge id               STP enabled     interfaces
    docker0         8000.0242759468cf       no              veth8c9bb82
                                                            vethd8f9afb



### Bridge网络示意图
![docker bridge network](/image/two-container-network.PNG)


* 图里veth1, veth2...可以理解成交换机上的网线插口

## 容器对外通信
容器和互联网通信需要先经过docker0的网关。docker0的“交换机”发现Ip 不在自己的路由表里就会走default 路径出去到external network。这个过程会涉及NAT, 发到外部网络的包里的source ip会变成是docker host的局域网ip。这个包到了家里wifi路由器时，才会转成真正的公网ip


## 自定义Bridge网络




## flask + redis 部署练习 和客户端连接容器内的DB/Cache
这个练习没啥难度。就是按着给的guide走就好。尝试理解下每一步在做什么。     
&nbsp;

我额外试了下在启动redis容器时，添加 -p 6379:6379 去转发暴露的redis端口。最后在windows的anotherRedisManager能通过VM_ipaddress: 6379成功连上容器里的redis。     
我也试了启动官方MySQL image时转发容器暴露的端口3306。也同样在windows的MySQL workbench里连接到了容器里的DB。

