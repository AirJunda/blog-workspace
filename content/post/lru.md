---
title: "LRU算法复习笔记"
date: 2021-12-30T22:39:12+08:00
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
我思维习惯把后面添加的node放到list的右边。左边是最早入列 aka 出局时第一个死的。
为了写起来方便。记得下面这几点：
* Node里同时存key和value的值
* 整2个dummy, 一个叫head，一个叫tail; 初始化双向linked list时就包含这2个dummy。之后set的新node都夹在head和tail之间。**head和tail并不是队列里的head和tail,而是dummy**
* 可以单独建个类叫DoubleLinkedList做一些封装。也可以只定义node。其他所有的helper method全放到Class LRU里
* size建议直接获取hashmap的size 而不是自己维护size变量
* 除了set/get外的辅助函数：add(k,v OR node), pop(node), move2tail(node), popfront()
* pop(node)不需要把这个node的prev, next都砍掉

写的时候我有自己思考过，hashmap的del/set是放到set(), get()里，还是放到add(), pop(), popfront() ?  我觉得其实都可以。如果封装一个doubleList 的类。就放到set/get里。因为hashmap的维护是LRU这个类才去做。

这一版我写的。我是把hashmap操作写在pop(), add(), move2tail() 这些helper里
```python
class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.next = None
        self.prev = None

    def __str__(self):
        return str(self.key) + ": " + str(self.val)

class LRUCache:
 
    def __init__(self, capacity):
        self.capacity = capacity
        self.key2node = dict()
        self.head = Node('h', 'head')
        self.tail = Node('t', 'tail')
        self.head.next = self.tail
        self.tail.prev = self.head

    def getsize(self):
        return len(self.key2node)

    def popleft(self):
        self.pop(self.head.next)

    def pop(self, node):
        prevn = node.prev
        nextn = node.next
        prevn.next = nextn
        nextn.prev = prevn

        #node.next, node.prev = None, None #不必要的砍断
        del self.key2node[node.key]
        return node

    def add(self, node):
        # assume the key not in the LRU
        lastnode = self.tail.prev
        lastnode.next = node
        node.prev = lastnode
        self.tail.prev = node
        node.next = self.tail

        self.key2node[node.key] = node

    def move2tail(self, node):
        self.pop(node)
        lastnode = self.tail.prev
        lastnode.next = node
        node.prev = lastnode
        self.tail.prev = node
        node.next = self.tail

        self.key2node[node.key] = node

    """
    @return: An integer
    """
    def get(self, key):
        if key not in self.key2node: return -1
        targetnode = self.key2node[key]
        self.move2tail(targetnode)

        return targetnode.val

    """
    @return: nothing
    """
    def set(self, key, value):
        # if key not in LRU
        if key not in self.key2node:
            # if capcity is full
            if self.capacity == self.getsize():
                self.popleft()
            self.add( Node(key,value) )
        else: # key in LRU
            node = self.key2node[key]
            node.val = value
            self.move2tail(node)
            
```

&nbsp;

## 另一种写法

下面这个是labuladong的思路。把double linked list单独封装。把pop(node), add(node)，popfront()放到double linked list，把map的操作放到LRU里
```python
class Node:
    def __init__(self, key, val, next=None, prev=None):
        self.key = key
        self.val = val
        self.next = next
        self.prev = prev


class DoubleList:
    def __init__(self):
        # self.head和self.tail都充当dummy节点（哨兵节点）
        self.head = Node(-1, -1)
        self.tail = Node(-1, -1)
        self.head.next = self.tail
        self.tail.prev = self.head
        self.size = 0

    def addFirst(self, x):
        """在最前面加个节点x，注意语句顺序，很经典！"""
        x.next = self.head.next
        x.prev = self.head
        self.head.next.prev = x
        self.head.next = x
        self.size += 1

    def remove(self, x):
        """删除节点x，assume调用时说明x一定存在"""
        x.prev.next = x.next  # 像一个顺时针
        x.next.prev = x.prev
        self.size -= 1

    def removeLast(self):
        """
        删除链表中最后一个节点，并返回该节点
        注意双向链表的删除时间复杂度是O(1)的，因为立刻能找到该删除节点的前驱
        """
        if self.size == 0:
            return None
        last_node = self.tail.prev
        self.remove(last_node)
        return last_node

    def getSize(self):
        return self.size


class LRUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.map = {}
        self.cache = DoubleList()

    def get(self, key: int) -> int:
        if key not in self.map:
            return -1
        val = self.map[key].val
        self.put(key, val)
        return val

    def put(self, key: int, value: int) -> None:
        new_item = Node(key, value)
        if key in self.map:
            self.cache.remove(self.map[key])
            self.cache.addFirst(new_item)
            self.map[key] = new_item
        else:
            if self.capacity == self.cache.getSize():
                last_node = self.cache.removeLast()
                self.map.pop(last_node.key)
            self.cache.addFirst(new_item)
            self.map[key] = new_item

```

## 链表的Debug
为了更方便的debug，我写了一些链表debug的utils. 

```python
def debug(head):
    # print the list
    arr = []
    strs = ""
    while head:
        arr.append(head.val)
        print( str(head.val),"->", end=" ")
        head = head.next
    print('null')


def build(arr):
    # genereate linked-list from array
    dummy = ListNode(-1)
    h = dummy
    for v in arr:
        newnode = ListNode(v)
        h.next = newnode
        h = h.next
    return dummy.next
```