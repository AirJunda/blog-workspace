---
title: "那些年 Celery 踩过的坑"
date: 2022-03-19T21:30:36+08:00
draft: false
tags: ["Celery","Python","Tech","Linux"]
topics: ["TechBlog"]
---

## 初见Celery
下一家公司要用Celery, 于是决定在家自学。结果没想到看着官方文档很简单，其实有不少隐藏的坑。这篇文章就是记录下自己踩过的坑和排查经历。至于Celery是什么的内容，不在本文介绍。

Celery教程：
https://www.celerycn.io/ru-men/celery-chu-ci-shi-yong

## Windows的坑
在参考上面的教程时，我用的redis做broker。启动celery worker后，发现terminal显示了message received, 但是worker不执行相应的消息处理。搜索Google的解决方案，发现加上下面的 -P solo 参数启动worker可以解决：
```shell
celery -A tasks worker --loglevel=info -P solo
```


但是worker只能执行一次消息的处理。后续的消息都没反应。并且用ctrl + c无法终止worker, 会挂死，只能关掉terminal。

### 排查结论
最后发现[有人](https://www.pythonf.cn/read/121936)提到了celery对windows的支持已经没了。celery >= 4的版本在windows下看起来是有bug的。并且感觉这个bug和win下的celery并行有关。于是决定试一试Linux下celery行不行。

## Ubuntu的坑
改用Ubuntu的VM并不是一帆风顺的，遇到了好几个问题。最后都解决了。

### Python3的版本
之前的vagrantfile一直用的Ubuntu 16的版本，但是16的版本呢，python3装的是3.5。我尝试网上的教程安装3.8，但是就死活说找不到 python3.8. 有人说是因为16的版本太旧了。装不了3.8和3.9。
而3.5的话，import新的celery会报语法错误。于是最后折腾了半天只好卸载了python3.5,装了3.6。然而这个带来了另一问题，就是pip3也卸载了。而重新装pip3会主动安装python3.5-minimum. 最后的解决方案是单独装pip3.6, 之后py3的包都用pip3.6而不是pip3去管理，或者设置soft link让pip3指向pip3.6。 这个过程涉及了好几个步骤，记录如下：

#### 安装python3.6
```shell
apt-key adv --refresh-keys --keyserver keyserver.ubuntu.com
apt-get -y install software-properties-common
add-apt-repository ppa:deadsnakes/ppa
apt get update
apt-get -y install python3.6
apt remove python3.5-minimal -y #有人说不建议删除3.5，有些系统工具会用不了。我当时是删了。  
```

#### 指定python3为python3.6
设置soft link, 让python3指向python3.6的bin
```shell
which python  #找出python的bin path, 一般在/usr/bin
ln -sf /usr/bin/python3.6 /usr/bin/python3
```
#### 安装pip3.6
[参考教程](https://ashish.one/blogs/install-python3.6-pip3.6-pipenv-on-ubuntu14.04/)  但是参考教程里的get-pip.py脚本只支持3.7以上的。所以要装3.6的话，get-pip.py要用下面的url
```shell
wget https://bootstrap.pypa.io/pip/3.6/get-pip.py
sudo python3.6 get-pip.py
sudo ln -sf /usr/local/bin/pip3.6 /usr/bin/pip3 #留意pip3.6的位置可能不一样
```
### Windows下的Pycharm设置VM中的运行环境
上面搞完了后，我们的VM是ok了。但是我们需要在Pycharm里设置项目的interperter 为vagrant创建的VM中的python3.6的bin path。
[教程](https://iswbm.com/357.html)

####项目文件夹同步
我目前是把project folder放在vagrantfile所在的目录下。我尝试去把windows下的任意位置和VM中的路径做同步绑定。但是没成功。这个等以后成功了，再在这里补充。

### VM访问Host的服务
我的redis是跑在Win上的。那么linux里运行的celery的broker地址就不能用localhost了。因此需要找出host的地址。但是我这里卡住了，我在VM里用Ifconfig找host的地址，但试了几个都对。我最后是通过在host启动了一个flask app, 并设置app.run(host="0.0.0.0")，允许所有host访问当前的flask。这样flask启动后console里会打印出host的地址。我当时显示的host地址是 http://192.168.1.42:5000/
最后就用这个host地址去配置celery的conf

### 配置Redis允许所有host访问
Win下的Redis安装目录下有2个conf文件。网上说只要改redis.windows-service.conf就行。我当时是2个都改了。修改如下
```
1- bind 0.0.0.0 instead of 127.0.0.1

2- Change protected-mode to no
```

### It Works!
到这里，我的celery demo终于成功跑起来了。并且在vm的terminal里能看到消息被消费执行后输出的结果。在producer那边也能看到没有被阻塞。

