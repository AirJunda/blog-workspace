---
title: "Docker: Mount and Volume"
date: 2021-12-17T22:39:12+08:00
draft: false
tags: ["Docker","Tech", "Container"]
topics: ["TechBlog"]
---

## Docker数据持久化
默认情况下，在运行中的容器里创建的文件，被保存在一个可写的容器层。如果container只是停止。容器里写入的数据依然在。但是如果把容器给停了。容器里的数据就会丢失。为了永久保存容器里的数据(日志, DB etc.), 我们需要持久化容器里的数据到local host上，或者remote machine上，比如另一台服务器或者AWS S3


Docker主要提供了两种方式做数据的持久化
* Data Volume, 由Docker管理，(/var/lib/docker/volumes/ Linux), 持久化数据的最好方式
* Bind Mount，由用户指定存储的数据具体mount在系统什么位置

本笔记主要记录的是2种持久化方式。Data Volume和Bind Mount

## Data Volume
volume在host上创建。创建和管理是由docker负责的。每个容器持久化的数据存到各自的volume。使用volume方式时，需要在容器的dockfile里添加 下面这样的命令：     
```dockerfile
VOLUME ["/filepath_in_container"]
```     
容器启动后，会在host中生成一个volume。可以通过 ```docker volume ls```  查看到新创建的volume。常用命令：
* docker volume ls
* docker volume inspect xxxx (xxx is volume name)

inspect命令可以给出这个volume在host上的path, linux下cd 到那个path就可以看到容器里持久化目录的内容。windows则看不到。因为windows下其实是存放到一个VM中。


这样方式有一个缺点，就是每次容器创建时都会创建一个新的volume。如果我们想修改image后，运行的容器依然是使用同样的volume, (比如mysql 的容器重新部署后，依然使用原来已经持久化的DB数据) 那么我们需要在container run时指定volume的名字。首次运行会创建一个该名字的volume。之后每次的运行都会指向同名的volume而非再次创立新的volume。需要加上 ```-v vol_name: path_in_container```

例子如下：       
 ``` shell
 docker container run --name volume-mysql -e  -v mysql-data:/var/lib/mysql mysql:5.7
 ```
**注意**：需要dockfile里声明 
```dockfile
VOLUME ["/var/lib/mysql"]
 ```





## Bind Mount
这种方式就是指定host上的一个filepath和容器里的某个路径做映射。类似于vagrant里把vagrant file目录和VM里的/vagrant做映射。这个方式在Windows上比使用data volume好用多了。不过需要额外设置下window上的docker for desktop里file share设置。具体看回udemy的视频。

用法：```-v  path_in_host: path_in_container```

上课演示的命令如下：

 ``` shell
 docker container run --name mount-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d -v /vagrant/mount:/var/lib/mysql mysql:5.7
 ```

注意上面的 -v 的写法是 /vagrant/mount:/var/lib/mysql
前面是host的mount文件夹路径。“:”后面跟着的是容器里的target path

### Bind Mount的实践例子
上课演示的例子是这样的。host机没有C++编译环境。但是我们有一个C++的编译环境。我们可以运行一个C++编译环境的容器，bind mount到本地目录。把需要编译的代码放到本地Mount目录里。在container中调试编译。然后生成的bin文件在host中也能获取到。直接可运行。


## VS Code Remote Container插件
安装这插件能极大帮到涉及container开发时的体验。课上的例子是基于windows环境下直接开发的例子。我目前是在win的VM下运行容器。暂时用不到。但是我个人觉得可以帮到我在mac下开发的情景。

## 环境清理
强制删除所有容器，系统清理和volume清理

* docker rm -f $(docker container ps -aq)
* docker system prune -f
* docker volume prune -f