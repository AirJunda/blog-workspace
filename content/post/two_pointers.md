---
title: "Two_pointers"
date: 2022-02-01T17:51:43+08:00
draft: true
tags: ["Algo","Tech","Lintcode","Two Pointers"]
topics: ["TechBlog"]
---


## Two Pointers问题
严格来说two pointers不是一种算法。是一类问题。主要是可以分成 背向，双向，同向。


### 背向双指针
用这题复习就好了：[最长回文字串](https://www.lintcode.com/problem/200/solution/16578)       


 ### 双向双指针
 经典例题是two sum，但是 双向双指针其实可以细分为3类。
 1. Reverse 类（较少）
2. Two Sum 类（最多）
3. Partition 类（较多）

##### Reverse 类
复习一下三步翻转法就好: [旋转数组](https://www.lintcode.com/problem/1334/description)  

图解如下：      
```java
nums = "----->-->"; k =3
result = "-->----->";
reverse "----->-->" we can get "<--<-----"
reverse "<--" we can get "--><-----"
reverse "<-----" we can get "-->----->"
```

##### Two Sum 类
TwoSum原题：给一组整数arr和目标值，是否存在任意一对数字之和等于这个值
原题很简单，但是如果输入有重复元素应该如何处理呢？那么就看这题：
[Two Sum - Unique pairs](https://www.lintcode.com/problem/587)  
```python
def twoSum6(self, nums, target):
        # write your code here
        nums.sort()
        ans = 0
        i,j = 0 , len(nums)-1
        while i < j:
            tsum = nums[i] + nums[j]
            if tsum > target: j -= 1
            elif tsum < target: i += 1
            else:
                ans += 1
                i += 1
                j -= 1
                while i < j and nums[i] == nums[i-1]:
                    i += 1
                while i < j and nums[j] == nums[j+1]:
                    j -= 1

        return ans
```




[3Sum with repeated elements](https://www.lintcode.com/problem/57/)
```python
def threeSum(self, numbers):
        numbers.sort()
        ans = []
        for k in range(2,len(numbers)):
            if k < len(numbers)-1 and numbers[k+1] == numbers[k]:
                # 枚举c, 这里是c的去重
                continue

            target = -1* numbers[k]
            i,j = 0,k-1
            while i < j:
                if numbers[i] + numbers[j] == target:
                    ans.append((numbers[i],numbers[j],numbers[k]))
                    i += 1
                    j -= 1
                    # 下面这里是a,b的去重
                    while i < j and numbers[i] == numbers[i-1]:
                        i += 1
                    while i < j and numbers[j] == numbers[j+1]:
                        j -= 1
                elif numbers[i] + numbers[j] < target:
                    i += 1
                else:
                    j -= 1

        return ans
```
另外还有一些题目是找离target最近的pair/pair个数/diff:
* [两数和的最接近值](https://www.lintcode.com/problem/533/)
* [两数和-小于或等于目标值](https://www.lintcode.com/problem/609/)
* [ 最接近的三数之和](https://www.lintcode.com/problem/59/)
 

##### Partition 类



 
