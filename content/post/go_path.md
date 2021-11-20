---
title: "GoPath和Go的包管理"
date: 2021-11-11T00:53:20+08:00
draft: true
tags: ["Go","Tech"]
topics: ["TechBlog"]
---

# 安装Go的必要设置之GoPath
这篇文章想讲一下GoPath这个东西。每次新电脑安装go是需要设置GoPath的。现在的安装一般会默认设置好。但是很多教程里会手动修改GoPATH到自己喜欢的目录。那么这个GoPath到底是做什么的呢？

## 他山之石
* [Go语言GOPATH是什么](https://www.cnblogs.com/ailiailan/p/13454139.html#:~:text=GOPATH%20%E6%98%AFGo%20%E8%AF%AD%E8%A8%80%E4%B8%AD,%E5%B0%B1%E6%98%AF%E4%BD%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8C%BA%E3%80%82)

* [Create projects independent of $GOPATH using Go Modules](https://medium.com/mindorks/create-projects-independent-of-gopath-using-go-modules-802260cdfb51)
* [让你的Golang项目在IDE里跑起来](https://cloud.tencent.com/developer/article/1596713)
* [Go语言基本环境变量与依赖管理](https://cloud.tencent.com/developer/article/1650021?from=article.detail.1596713)  

## 我的理解

#### GoPath的古早时代（现在基本不用了。但是可以了解）
很久很久以前，在遥远的银河系.....有一帮人整出了go语言。然后开始有人使用go开发。但是早期go开发需要把代码放到gopath路径中。gopath在安装时会自动设置。gopath下需要有3个文件夹，src,bin,pkg; 我们的每个project folder都放到src路径下。

那时候，要导入一个go的dependency，需要使用go get命令。使用go get 拉取外部dependency会自动下载并安装到$GOPATH/src 目录下。

不过go get无法解决导入的dependency的版本控制。因为go get会把同一个依赖都存在同一个路径下面。所以go get 拉取的都是最新版。如果dependency新版不兼容了，就很麻烦。为了解决版本控制，先是有了govendor,但是现在已经普遍使用go mod了。Govendor我们直接忽略讲解但后面会讲mod

#### Project GoPath
goland开启一个新的go的项目时，会让你设置global GoPath和Project GoPath。影响的主要是包的导入时该搜索哪里的问题。

import导包规则是：

1. 先去GOROOT/src/路径下找      

2. 如果1没有，就去Project GOPATH/src/路径下找     

3. 如果2还没找到，就去Global GOPATH路径下找(Global GOPATH一般是设置的系统变量里的那个GoPath)

Project GoPath 代表项目所使用的 GoPath，该设置会被保存在工作目录的 .idea目录下，不会被设置到环境变量的 GoPath 中，但会在编译时使用到这个目录。（注意，GoPath需要目录内有src文件夹, 因此一个gin项目文件应该放在Project GoPath/src下面。所以Project GoPath不是gin项目的root folder)

对于Project GoPath 我其实还是不是有的地方不懂。网上提到说，如果下载了别人的go项目，但是提示cannot find package "server/common/dbutil”，那就设置一下project GoPath。

#### GoPath和import时的路径关系
无论是哪种GoPath, GoPath就是我们导入包时的根目录。所以在代码里导入某个GoPath里存在的包时(第三方或者自定义的如/dao/db)，就不用写前面一大串路径了。比如 要使用$GOPATH/src/blue/red这个包,那代码里就是：     
```python
import "blue/red"
```
对于github下载的包，go get到本地时会保留github.com/xx/xx这样完整的路径。


Packages that live in source code repositories, like GitHub and BitBucket, have the full location of the repository as part of their import path.

For example, we would import the source code at https://github.com/gobuffalo/buffalo using the following import path:
```go
import "github.com/gobuffalo/buffalo"
``` 
Therefore, this source code would be in the following location on disk:
```
$GOPATH/src/github.com/gobuffalo/buffalo
```


#### Go Modules
> *多年以后，奥雷连诺上校坐在显示器前，准会想起设置GoPath那个遥远的下午*

前面讲解了一堆关于GoPath的内容，其实在Module出现后都可以不关心了。Go mod的出现让我们可以不需要把project放到GoPath中，也不需要设置src/pkg/bin目录。甚至Go1.13开始不再推荐使用GoPath。

Go modules是Go的一个包管理工具，最低Go版本要求1.11+。可以理解为Python的虚拟环境，或者Java的maven。启用go modules后，包都会下载到%GOPATH%/pkg/mod文件夹中。(go get则是下载到src文件夹)

##### GO111MODULE 参数
这个环境变量是用来作为使用Go Modules 的开关。可以说这个变量是历史产物，很有肯能会在将来Go的新版本中去除掉。
* GO111MODULE="auto" # 只要项目包含了go.mod 文件的话启用Go modules， 在Go1.11-1.14 中是默认
* GO111MODULE="on"   # 启用
* Go ModulesGO111MODULE="off"  # 禁用Go Modules， 对老的项目进行兼容

##### Go.mod 文件
使用Go mod会在项目文件夹内生成go.mod文件。相当于requirement.txt文件，里面会声明一些所需要的依赖包。如如果在具有go.mod 的项目的文件夹下使用go get , 会将对应的依赖以及版本写入go.mod,并将包下载到%GOPATH%/pkg/mod。如果```go get```下载出错，用```go mod download```

任何一个新项目需要用 ```go mod init modulename``` 去生成go.mod 文件。如果project在gopath/src下，可以无参数 ```go mod init```, 否则需要指定modulename。名字可以随便写，建议和当前文件夹名字一样。或者公司有命名要求按公司要求，比如"tencent/pcg/project2021/v1"

##### Go mod 常用命令

* 在当前文件夹下初始化一个新的 module，创建 go.mod 文件:go mod init name  
* 拉取缺少的模块，移除不用的模块:go mod tidy
* 将依赖复制到 vendor 下:go mod vendor
* 下载依赖:go mod download
* 检验依赖:go mod verify
* 显示模块依赖图:go mod graph

#### Import的路径怎么写
import的路径可以是相对路径，比如那些在gopath/src的包;可以是modulename/xxx/xx; 可以是../或者./ 这样开头的路径;  
具体看这篇吧：
[一起弄明白go的依赖管理 Go Modues/ GOPATH](https://juejin.cn/post/6844904136056504327)  



## Summary
本文总结了GoPath和Go Modules的使用。Go Modules的出现可以代替GoPath。除非是遇到了一些legacy的项目。**有了Go Module将不需要在把项目放到$GOPATH/src下。** 建议直接学习modules的使用





## Reference:  
https://www.digitalocean.com/community/tutorials/understanding-the-gopath

https://cloud.tencent.com/developer/article/1778130