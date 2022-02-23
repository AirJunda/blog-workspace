---
title: "System Design 备忘录"
date: 2022-02-07T23:33:17+08:00
draft: false
tags: ["System Design","Tech"]
topics: ["TechBlog"]
---

## 系统设计文章收藏
系统设计想等我自己有空的时候在慢慢更新专门的笔记。这里暂时先记录一些我看到的不错的文章，做一些简单的标记和分类。


### High Availability 
方法论上，高可用是通过冗余+自动故障转移来实现的。整个互联网分层系统架构的高可用，又是通过每一层的冗余+自动故障转移来综合实现的

* [什么是HA](https://zhuanlan.zhihu.com/p/43723276)
* [VIP技术](http://www.xumenger.com/virtual-ip-20190220/)


### Availability vs Reliability 
Reliability is availability over time considering the full range of possible real-world conditions that can occur. If a system is reliable, it is available. However, if it is available, it is not necessarily reliable

https://pastebin.ubuntu.com/p/nstvfbnqYz/



### 优化写
Write-back cache: Under this scheme, data is written to cache alone, and completion is immediately confirmed to the client. The write to the permanent storage is done after specified intervals or under certain conditions. This results in low-latency and high-throughput for write-intensive applications; however, this speed comes with the risk of data loss in case of a crash or other adverse event because the only copy of the written data is in the cache.

Redundancy plays a key role in removing the single points of failure in the system and provides backups if needed in a crisis

## 微博关注列表的数据存储实现
https://cloud.tencent.com/developer/article/1451238