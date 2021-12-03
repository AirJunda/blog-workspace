---
title: "Win Terminal Setup"
date: 2021-11-29T21:10:13+08:00
draft: false
tags: ["Terminal","Tech","Windows","Setup"]
topics: ["TechBlog"]
---

## Win10 Terminal 美化工程

这篇记录下win10上美化Terminal的步骤。

## 他山之石
* [攻略](https://moedev.net/nice-winterminal/)
* [Windows Terminal 主题美化](https://juejin.cn/post/6933384126363877384)


## 记录
1. 先按上文第一篇安装了字体。解压后进入ttf文件夹。打开CascadiaMonoPL安装就好
2. 然后powershell里执行下面3个命令 

``` shell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
Get-PoshThemes
```

输入下面这个命令可以随时替换theme;但是terminal重启后就失效。
```
Set-PoshPrompt -Theme agnoster # 我现在用的paradox
```

3. 要想不失效，需要手动修改下面这个配置文件, 在cmd里敲
```
notepad $PROFILE
```
然后profile文件里添加这3行
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Agnoster/paradox/你想要的theme name


### 到这一步遇到的坑
设置完发现有乱码。paradox好一些，只有error符号乱码。其他主题连 ~ 都显示不出。试了各种PL字体都没用。最后解决方案是安装了Nerd字体就解决了。参考的是这篇：[攻略](https://tokisaki.top/blog/beautify-windows-terminal/)。其中windows 官方攻略也提到了安装nerd字体库可以解决。nerd网站下有很多字体，我最后安装的是Caskaydia Cove Nerd Font

### Windows Terminal的进一步配置
原文攻略还有后续的。但是我就没就继续做了。因为对目前效果已经满意。



## Side Effect
修改了windows terminal的样式会导致IDE中的集成terminal出现色彩不协调或者乱码。需要单独设置。

## VS Code中Terminal乱码解决
需要在File/Preference/Settings中设置Terminal Font. 在settings的搜索box中输入Terminal Font就能找到填写字体的框框。我Win Terminal用的是CaskaydiaCove NF，所以我在VS Code中也设置的是这个字体。但是也可以使用其他的。设置教程可以参考这篇：
[解决 VScode 终端 Zsh 主题乱码的问题](https://dreamhomes.top/posts/202006222027/)

## Goland中Terminal美化 

Goland中也会因为terminal字体美化而在内置terminal中出现不同的颜色。目前似乎没发现兼容方案。



## Extra Reading
[基于posh字体乱码问题修复](https://www.i4k.xyz/article/xuchaoxin1375/118269272)





