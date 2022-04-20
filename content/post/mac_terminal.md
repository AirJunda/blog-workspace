---
title: "Mac Terminal Setup"
date: 2021-11-29T21:10:13+08:00
draft: false
tags: ["Terminal","Tech","Mac","Setup"]
topics: ["TechBlog"]
---

## Mac Terminal 美化工程

这篇记录下mac上美化Terminal的步骤。本文是win terminal设置的姐妹篇。

## 他山之石
* [视频讲解](https://www.youtube.com/watch?v=riIfZZiHTx4&list=PLfQqWeOCIH4DaGhprgvGX_4nr2yosD3jP&index=1)
* [iTerm2 + Oh My Zsh 打造舒适终端体验](https://zhuanlan.zhihu.com/p/37195261)

## 记录
参考第二个link里的文章完成了setup。我没有做原文中的“安装配色方案”，其他都做了。

我选agnoster theme, 这个是我一直想要的theme。但是这个theme初始效果蓝色会比较深色。可以在iterm的preferences -> profiles -> colors -> color preset 里去调节整体的颜色方案。我最后选了pastel

如果想添加额外的color preset方案可以从github上导入。比如想导入下面的这个方案，可以看readme里的说明。
https://github.com/QuentinWatt/dark-flat-iterm-colors

## 可选设置
箭头里面路径太长的解决方式：
```shell
vim ~/.oh-my-zsh/themes/agnoster.zsh-theme
```
把下面代码里的%~修改成%1d 即可
prompt_dir() {
prompt_segment green $CURRENT_FG '%~'
}




## Extra Reading
* [额外可选配置](https://segmentfault.com/a/1190000013612471)
* [Customize Iterm 2 with ZSH and Powerlevel10k](https://www.youtube.com/watch?v=D2PSsnqgBiw)







