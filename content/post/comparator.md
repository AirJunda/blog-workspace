---
title: "Python自定义Comparator"
date: 2022-01-12T18:38:33+08:00
draft: false
tags: ["Algo","Tech","Lintcode","Python"]
topics: ["TechBlog"]
---

## Python中自定义comparator
有的题目需要排序时基于自定义的规则去排序。自定义规则通过手写comparator去实现。sorted(), sort()都可以传入自定义的compartor, 但是python3的使用上有点点不一样。因为没有了cmp参数。

### Lintcode相关题目
* [184 Largest Number](https://www.lintcode.com/problem/184/description)

### comparator
对于函数 comp(left, right)：
* return a negative value (< 0) when the left item should be sorted before the right item
* return a positive value (> 0) when the left item should be sorted after the right item
* return 0 if left and right input is equal

### 实战（184 最大数）
给出一组非负整数，重新排列他们的顺序把他们组成一个最大的整数。
```python
Input: [1, 20, 23, 4, 8]
Output: "8423201"
```
Python2
```python
class Solution:
    """
    @param nums: A list of non negative integers
    @return: A string
    """
    def largestNumber(self, num):
        #更简单写法 nums = sorted(num, cmp=lambda x, y: 1 if str(x) + str(y) < str(y) + str(x) else -1)
        nums = sorted(num,cmp=self.mycmp, reverse=True)
        if nums[0] == 0:
            return '0'
        tmp = [str(x) for x in nums]
        return "".join(tmp)

    def mycmp(self,a,b):
        # a < b return 1, > -1, equal,1
        if str(a) + str(b) < str(b) + str(a):
            return -1
        elif str(a) + str(b) > str(b) + str(a):
            return 1
        return 0
```
Python3
参入comparator 不能再用cmp=mycmp; 使用的是key参数，但是key不能传入函数。所以需要functools.cmp_to_key(func)去把函数转成key

因此需要**import functools**, 最后使用如下

```Python
nums = sorted(num,key=functools.cmp_to_key(mycmp), reverse=True)
```







