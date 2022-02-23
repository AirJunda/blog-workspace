---
title: "Django Get Started"
date: 2022-02-23T23:40:26+08:00
draft: true
tags: ["Algo","Tech","Django","Python"]
topics: ["TechBlog"]
---

## Django
本文主要是记录一些Django的初始概念。适用于第一次接触Django或者好久没碰Django后重新捡起来的复习。


### 新建Django Project

```shell
django-admin startproject project_name
```
### 新建Django App
```shell
python  manage.py startapp app_name
```

### Django Project vs Django App
初学Django这两个概念我有点懵，Gin或者Flask是没有类似Django里类似的划分的。我目前对Django Project vs Django App的理解是下面这样的。

首先是Django官网的解释：
An app is a web application that does something – e.g., a blog system, a database of public records or a small poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

我们其实可以看到APP相当于一个相对独立的功能模块，其遵循的设计理念是MVT（类似MVC）；在一个大型的Project中，可以包含很多APP，而且每个APP可以在不同的Project中复用，这也符合Django的编程理念，即：不要做重复的事情。

综上所述，project和APP的区别已经很明显了，project包含一些全局配置，这些配置构成一个全局的运行平台，各个APP都运行在这个全局的运行平台上，而APP代表的是一个相对独立的功能模块，所以程序的逻辑都在APP中。


