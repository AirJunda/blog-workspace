---
title: "那些Hugo踩过的坑"
date: 2021-11-04T22:39:37+08:00
draft: false
---

## Preface
部署网站这事就是个ops的活。虽然我工作的部门也是做ops的，但是我其实是不太喜欢做ops。无论是编程还是以前做实验，做ops经常会手忙脚乱。那怕按照给的文章或者guide去做，我依然会遇到一些奇奇怪怪的hurdles。这篇文章就是记录下我第一次上线这个网站时踩过的坑。

## 那些年踩过的坑
人类的本质是复读机。万事开头，先搜攻略。于是搜到了这2个，觉得比较有用。未来如果忘了hugo部署也可以直接从这2个攻略复习。
* [如何利用 GitHub Pages 和 Hugo 轻松搭建个人博客？](https://zhuanlan.zhihu.com/p/57361697)
* [使用 Hugo + Github 搭建个人博客](https://zhuanlan.zhihu.com/p/105021100?utm_source=wechat_timeline&utm_medium=social&utm_oi=545850779622232064&utm_content=first)

下面开始说按照上面的步骤时遇到的坑和注意事项。
### 发布前的阶段
安装好hugo后，主要做的事情是导入自己喜欢的theme，和用MD写自己的文章，然后在本地跑起来，预览下网站效果。

* 导入theme
  - 导入theme时，不同攻略中有2种方式：使用git submodule命令或者直接git clone。 建议使用 ```git submodule add``` 去导入。原因是如果使用action的CICD, 现有的yaml文件里需要指定theme文件是通过submodule去拉取。如果一开始没有添加theme路径为submodule, 在workflow的build阶段会报错找不到submodule theme/xxx。使用submodule的例子如下：
  > ```git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt```
* 创建新文章
  -  命令是这样写的 ```hugo new posts/demo.md ``` 但是很多theme默认文章是在 post/ 目录下。因此如果发现自己写的文章在网站启动后找不到，建议把上面命令里的posts改成post试一试。这个坑我搞了半个晚上才发现 XD

### 发布阶段
文章写好，本地调试后就要部署了。我们本地写的md文章其实是需要生成html,css,js等网页的文件然后才能发布成一个网站。不过这个任务就是Hugo去做了。Hugo解放了我们，让我们可以专心写md而不用管html这些细节。

生成网站文件的命令是 ``` hugo -D ``` 默认是在当前根目录下生成一个public文件夹。这个public里的内容才是部署需要的。到这一步时，项目文件的结构应该是这样的：
```
├── archetypes # 存放生成博客的模版
├── assets # 存放被 Hugo Pipes 处理的文件
├── config.toml # hugo 配置文件 
├── content # 存放 markdown 文件
├── data # 存放 Hugo 处理的数据
├── layouts # 存放布局文件
├── static # 存放静态文件 图片 CSS JS文件
├── themes # 存放主题
└── public # 存放生成的网页文件
```
这个根目录只是我们的workspace，我们要的结果其实是public文件夹(当然也可自定义输出文件夹名字)。有了这些后，就可以用github page去部署了。github page其实就是能将包含网页文件的repo给生成一个静态网站让他人访问。这样的repo通常会一个index.html文件作为站点索引。这个在public里能找到。


如果我们要使用github page去部署，根据github要求，这个网站的repo需要命名为 ```username.github.io```。那么问题来了：这个repo里放的是整个workspace还是只是public这个文件夹呢？

其实都可以。不同攻略里处理方式都不一样。有人把网站文件生成到/docs路径下。然后把整个workspace推上去，然后在github page的设置里指定/docs。这样同样能部署。但是有人提醒这样不安全。因为你的workspace别人也能看到。更好的方式是建2个repo。一个是workspace的repo。另一个是public文件的repo并且github.io结尾的repo是指向的后者。


上面我给的第一个link里就是这么处理的。然后还有个事要做，就是在workspace的repo 里把public folder设置为submodule：  
```git submodule add -b main https://url-to-github.io.git public```   
这么做据说是可以
> “当你 push worksapce库时，工作流自动将worksapce库的编译输出(/docs目录中的变更信息)合并io仓库，这样你就能隔离两个工作环境，还能随时存储你的工作进度”

我上面那句其实没完全懂，但是如果不设置submodule, 推送workspace会提示warning nested git repo，要求你设置成submodule。 所以最佳实践还是做一下吧。不要跳！看完这些，如果一些细节还是不太懂做了啥，可以看下面这个youtube demo:
[Creating a Blog with Hugo and Github in 10 minutes](https://www.youtube.com/watch?v=LIFvgrRxdt4)

### 发布后阶段
这个阶段我只遇到一个坑
* 如果public的repo里有readme, 会和index.html冲突。导致page显示的是readme而非index.html。Solution:
  - 在readme段首插入这三行<br>
  ---<br>
  permalink: /index.html<br> 
  ---<br>


到这一步，我踩过的坑都讲完了。最后放几个我当时看过的别人写的Hugo文章。

## Reading List
* https://cloud.tencent.com/developer/article/1769218 (架构图不错)
* https://zz2summer.github.io/github-pages-hugo-%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/ (有包含使用google analytics和插入图片水印的介绍)

