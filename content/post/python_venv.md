---
title: "Python 虚拟环境设置和使用"
date: 2022-02-23T18:39:17+08:00
draft: false
tags: ["Python","Tech", "Virtual Env","pipenv"]
topics: ["TechBlog"]
---

## 为什么需要虚拟环境？
在使用Python语言时，通过pip（pip3）来安装第三方包，但是由于pip的特性，系统中只能安装每个包的一个版本。但是在实际项目开发中，不同项目可能需要第三方包的不同版本，迫使我们需要根据实际需求不断进行更新或卸载相应的包，而如果我们直接使用本地的Python环境，会导致整体的开发环境相当混乱而不易管理，这时候我们就需要开辟一个独立干净的空间进行开发和部署，虚拟环境就孕育而生。

## Python虚拟环境搭建工具
Python的虚拟环境工具有好几个。一般接触的多的有virtualenv, venv, pipenv, conda; 我目前学习的是pipenv，对virtualenv有初步接触。未来可以在本文中继续补充相应的教程。

### Pipenv
pipenv所解决的问题:

* requirements.txt 依赖管理的局限
* 多个项目依赖不同第三方库、包版本问题
依赖分析

pipenv集成了pip，virtualenv两者的功能，且完善了两者的一些缺陷。
过去用virtualenv管理requirements.txt文件可能会有问题，Pipenv使用Pipfile和Pipfile.lock，后者存放将包的依赖关系，查看依赖关系是十分方便。


pipenv的安装方式如下:
```shell
pip3 install pipenv
```
如果是windows中安装需要添加如下的环境变量，才能在安装完后直接从cmd运行。注意下面路径中的xxx表示当前用户名，Python38是假定本机使用的python是3.8。
```shell
“C:\Users\XXX\AppData\Roaming\Python\Python38\Scripts”
```

#### pipenv常用命令：
```shell
pipenv --two  # 使用当前系统中的Python2 创建环境
pipenv --three  # 使用当前系统中的Python3 创建环境
pipenv --python 3  # 指定使用Python3创建环境
pipenv --python 3.6  # 指定使用Python3.6创建环境
pipenv --python 2.7.14  # 指定使用Python2.7.14创建环境
pipenv --rm # 删除当前目录下的虚拟环境
```

激活虚拟环境
```shell
cd project_folder
pipenv shell
```
**注意：**
* 创建环境时应使用系统中已经安装的、能够在环境变量中搜索到的Python 版本，否则会报错。
* 每次创建环境都会在当前目录下生成一个名为Pipfile文件，用来记录刚创建的环境信息，如果当前目录下之前存在该文件，会将其覆盖。




## Reference
https://zhuanlan.zhihu.com/p/163023998
https://blog.csdn.net/weixin_40922744/article/details/103723069