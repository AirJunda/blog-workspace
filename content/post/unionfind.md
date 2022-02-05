---
title: "Union Find 复习"
date: 2022-02-04T01:55:43+08:00
draft: false
tags: ["Algo","Tech","Lintcode","Union Find", "Graph"]
topics: ["TechBlog"]
---

## Union Find
一种用于解决一个图的2个点是否相互连通的数据结构。比BFS高效。复习可以用下面这2题来回忆:
[589 连接图](https://www.lintcode.com/problem/589/description)
[590 连接图 II](https://www.lintcode.com/problem/590/description)

运用：
[434 · 岛屿的个数II](https://www.lintcode.com/problem/434/)

UnionFind的模板写法如下：
```python
class UnionFind:
    def __init__(self, N):
        self.root = [i for i in range(N)]
        self.cnt = N
        
    def find(self, k):
        if self.root[k] == k:
            return k
        self.root[k] = self.find(self.root[k])
        return self.root[k]
    
    def union(self, a, b):
        x = self.find(a)
        y = self.find(b)
        if x != y:
            self.root[y] = x
        self.cnt -= 1
        return
```


并查集原生操作：
* 合并两个元素所在的集合 Union
* 查询元素的老大是谁 Find
 查询两个元素是否在同一个帮派

并查集的派生操作：
* 查询某个元素所在集合的元素个数
* 查询当前集合的个数


## 易错点
如果题目要求帮派的数量。有2种做法。
* 用一个 self.cnt记录孤立帮派数量。每次调用union() 操作后，self.cnt -= 1，最后self.cnt的值就是帮派数量
*  调用下面这个ngroup()

```python
    def ngroup(self):
        uniq = set()
        for p in self.root:
            uniq.add(self.find(p))
        return len(uniq)
```
易错点涉及的题目：[1179 朋友圈](https://www.lintcode.com/problem/1179/note)

