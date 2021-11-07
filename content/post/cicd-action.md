---
title: "CICD Action"
date: 2021-11-07T22:39:12+08:00
draft: false
tags: ["Hugo","Tech"]
---

## 使用GitHub Action 构建本博客的CICD发布流程
之前写Blgo, 每次发布前都需要```Hugo -D ```去生成发布后的files到Public文件下，然后手动去到Public路径下，commit再push上去。简单但枯燥。其实这个事情完全可以自动化做成流水线。每次Hugo workspace的repo发生commit，可以自动触发workflow去更新自己的page repo进而更新网站。    

构建的这个流水线就是本文要谈论的CICD workflow; 我这次使用的是github action这个CICD工具去构建

## 他山之石
和以前一样，先搜了别人的攻略，复刻，再总结。这次参考的攻略是下面这个：

* [Hugo使用Github Action自动部署博客到Github Pages](https://tomial.github.io/posts/hugo%E4%BD%BF%E7%94%A8github-action%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2%E5%8D%9A%E5%AE%A2%E5%88%B0github-pages/)

注意事项：
*  部署可以用personal token或者deploy key; 上面攻略是用personal token, 我最后也是用这个
* 原文提到"在源码repo里新建一个github-actions分支", 这个不需要单独新建一个branch, 可以在原来的main branch里操作，添加 .github/workflows/confg.yaml; config.yaml的文件名可以随便取，反正是yaml/yml结尾就行
* 原文里生成html是用```hugo --minify```, 替换成 ```hugo -D```

我最后成功部署的配置文件如下
```yaml
name: Deploy GitHub Page

on:
# 在push或pull请求事件上触发工作流
  push:
    branches:
      - main

jobs:
# 此工作流程包含一个名为deploy的job
  deploy:
    name: deploy github page
    runs-on: ubuntu-latest
    # steps of the job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
        fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod 

    - name: Setup Hugo Dependency
      uses: peaceiris/actions-hugo@v2
      with:
        hugo-version: '0.80.0'
        # extended: true  # use extened hugo if set true

    - name: Build
      run: hugo -D

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3  #一个自动发布github pages的action
      with:
        external_repository: AirJunda/AirJunda.github.io
        personal_token: ${{ secrets.ACTIONS_DEPLOY_KEY }}  # or use github_token: ?
        publish_dir: ./public # 注意这里指的是要发布哪个文件夹的内容，而不是指发布到目的仓库的什么位置，因为hugo默认生成静态网页到public文件夹，所以这里发布public文件夹里的内容
        publish_branch: main  # 发布到哪个branch
```

如果看别的攻略，你会发现哪怕是部署Hugo, 每个人的config也会有些差异。我觉得这个都不影响config里写的就是用yaml去定义了一系列的steps, 类似ansible playbook。我会把之前看的别人的攻略放到下面reading list里。以后如果想深入学习CICD via Action时再去研究

## Workflow Status
想知道自己的workflow有没有成功运行？想知道网站有成功重新部署？这个可以在hugo blog repo里的action tag下看到。

![](/static/image/action_status.jpg)

到这里，CICD的攻略就结束了。虽然是跑起来了，但是感觉CICD还是很多不清楚的地方。比如其实挺想知道如果是像公司那样用jenkins,cicd有应该怎么写。这些都留给以后了

## 未来计划
* 使用circle CI 去部署
* 未来将博客部署到AWS/GCP/AliCloud时再写一套使用云的CICD config

## Reading
* [使用deploy key的攻略](https://dwye.dev/post/hugo-github-action/)
* [GitHub Action 打造自动构建部署 CICD](https://www.bilibili.com/read/cv9776049)



