---
title: "Binary Search易错难点"
date: 2022-01-25T18:10:21+08:00
draft: false
tags: ["Algo","Tech","Lintcode","Binary Search"]
topics: ["TechBlog"]
---

二分法本身不难。不过有几类类问题我还不是特别熟练。容易卡住/出错。这个文章就是记录下相关的题。方便以后复习。

## 倍增法
* [447  在大数组中查找](https://www.lintcode.com/problem/447/note)

##  二分答案
二分答案容易出Hard. 这类题难的是要么想不到可以用二分法做，要么就是二分用的helper()不知道做什么。二分答案的helper()很多题里都是一种greedy的思路。

[931](https://www.lintcode.com/problem/931/)这题号称集二分法之大成的经典题

[二分答案合集](https://www.lintcode.com/problem-tag/419/)





##  旋转数组相关
 寻找旋转排序数组中的最小值
* [159](https://www.lintcode.com/problem/159/)
* [160](https://www.lintcode.com/problem/160/)

搜索旋转数组中的值
* [62](https://www.lintcode.com/problem/62/)
* [63](https://www.lintcode.com/problem/63/description)


### 159 寻找旋转排序数组中的最小值（无重复）
159这题 我觉得难点是思考的全面性。比如binary search里判断条件是 > 还是 >=；但是这题是假设no repeated elements, 所以其实不难。


```python
class Solution:
    """
    @param nums: a rotated sorted array
    @return: the minimum number in the array
    """
    def findMin(self, nums):
        # 159 solution
        l, r = 0, len(nums)-1
        leftmin, rightmax = nums[0], nums[-1]
        while l + 1 < r:
            m = (l + r) // 2
            if nums[m] >= rightmax:
                l = m
            elif nums[m] < rightmax:
                r = m

        return min(nums[r],nums[l],leftmin)

```

### 160 寻找旋转排序数组中的最小值（有重复）
160这题的坑是这样的：如果沿用159的做法会错。因为有重复元素后，如果旋转点刚好是在一群repeated element中间。就会形成新的nums里nums[-1]==nums[0]

比如：
[1,1,-1,1]
[999,999,1000,1000,10000,0,999,999,999]

出错的原因是当```nums[mid] == v```时，我们无法确定mid是在断点左边的山坡还是右边的山坡。

解决办法
假设这个左右都出现的重复元素是v,我们先把nums左或者右的任意一边的v全消掉。这样就避免了当```nums[mid] == v```时，我们无法订位在哪个坡的问题。
我的方法是在进入二分前，先把左边的v全砍掉：

```python
def findMin(self, nums):
        # 160
        arr = nums
        l, r = 0, len(nums)-1
        while l  < r and  nums[l] == nums[r]:
            # 砍掉左边的v
            l += 1

        rightmax = nums[r]
        while l + 1 < r:
            m = (l + r) // 2
            if nums[m] > rightmax:
                l = m
            elif nums[m] <= rightmax:
                # 有了上面的砍断，这里用 <=就是安全的
                r = m

        return min(rightmax,arr[l],arr[r])
```

