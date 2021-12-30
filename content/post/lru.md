---
title: "LRU算法复习笔记"
date: 2021-12-31T22:39:12+08:00
draft: false
tags: ["Algo","Tech","Lintcode"]
topics: ["TechBlog"]
---

## LRU算法
这篇是记录下LRU算法写起来时候的一些注意事项。LRU算法在OS, DB中都有涉及。在Redis也有涉及。面试也是高频的一个考察。       
LRU 的全称是 Least Recently Used，也就是说我们认为最近使用过的数据应该是是「有用的」，很久都没用过的数据应该是无用的，内存满了就优先删那些很久没用过的数据。

## 他山之石
* [labuladong解析](https://leetcode-cn.com/problems/lru-cache/solution/lru-ce-lue-xiang-jie-he-shi-xian-by-labuladong/)


&nbsp;

## 分析
LeetCode的LRU需要下面2个这样的借口。要求：
* o(1)操作
* 满了capacity时，set() 时候需要踢掉最久未使用的key-value pair来腾空间
* 每次get, 需要把key-value挪到某种排序的最前面。


```python
def __init__(self, capacity):
    pass
def get(self, key):
    pass
def set(self, key, value):
    pass
```
那么，什么数据结构同时符合上述条件呢？哈希表set/get快，但是数据无固定顺序；链表有顺序之分，set插入快，但是get慢。所以结合一下，形成一种新的数据结构：哈希链表。

![哈希链表](https://pic.leetcode-cn.com/b84cf65debb43b28bd212787ca63d34c9962696ed427f638763be71a3cb8f89d.jpg)

哈希链表就是一个双向linkedlist + hashmap；hashmap的key是key, value是链表里的node。这样可以直接通过某个Key访问到node

## 实施
为了写起来方便。记得下面这几点：
* Node里同时存key和value的值
* 整2个dummy, 一个叫head，一个叫tail; 初始化双向linked list时就包含这2个dummy。之后set的新node都夹在head和tail之间
* 可以单独建个类叫DoubleLinkedList做一些封装。也可以只定义node。其他所有的helper method全放到Class LRU里


## 使用OrderDict的偷懒实现
OrderDict是Python中的特殊哈希表。会按插入先后顺序维持一个序列。最先添加的位于最左，后面的加到右边。**这个版本里需要记忆orderdict丢弃第一个key的特殊用法： dict.popitem(last=False)**

```python
from collections import OrderedDict
class LRUCache:
    """
    @param: capacity: An integer
    """
    def __init__(self, capacity):
        self.capacity = capacity
        self.maps = OrderedDict()
    
    """
    @param: key: An integer
    @return: An integer
    """
    def get(self, key):
        if key not in self.maps: return -1
        res = self.maps[key]
        # 因为get时，要更新key所在的顺序位置，所以先要删除它
        self.maps.pop(key)
        # re insert into the linkedlistmap
        self.maps[key] = res
        return res

    """
    @param: key: An integer
    @param: value: An integer
    @return: nothing
    """
    def set(self, key, value):
        # 1. check key在不在
        # 2. check 有么有满了


        if key not in self.maps:
            if len(self.maps.keys()) == self.capacity:
                #  orderdict中丢弃最左的第一个k-v的特殊写法
                self.maps.popitem(last=False)

        else:
            self.maps.pop(key)
        self.maps[key] = value
            
```

但是面试时候，通常会让你从头用双端队列实现。

## Double Linked List实现

