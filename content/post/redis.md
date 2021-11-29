---
title: "Redis Get Started on Windows 10"
date: 2021-11-23T22:14:59+08:00
draft: false
tags: ["Redis","Tech","Windows"]
topics: ["TechBlog"]
---

## Win10安装Redis

这篇记录下win10上安装redis的一些要点

## 他山之石
* [win10下安装redis服务](https://www.cnblogs.com/huchong/p/9650158.html)


## 要点
* 按照上文的启动redis后，打开一个新的cmd窗口，设置redis as service

* 把redis的解压目录添加到环境变量中。然后重启一下terminal，之后就不用每次都进到redis安装目录才能运行redis-cli

* 上面2步都做了后，以后启动redis只需要cmd里敲：
```shell
redis-server --service-start
```
然后进入redis的CLI界面就敲：
```shell
redis-cli -h 127.0.0.1 -p 6379 
```
停止redis service:
```shell
redis-server --service-stop
```
## Redis GUI
我用的是Another Redis Desktop Manager; 免费，Win/Mac都可以用

设置connection的话，默认的端口一般是6379

## Redis分别式集群
看这篇了解下。以后再尝试：
[redis的安装和分布式配置简洁教程](https://zhuanlan.zhihu.com/p/25239636)