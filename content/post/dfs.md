---
title: "DFS 复习"
date: 2022-02-01T15:49:36+08:00
draft: false
tags: ["Algo","Tech","Lintcode","DFS"]
topics: ["TechBlog"]
---

## DFS
这篇是记录下DFS相关的题写起来时候的一些注意事项。DFS个人觉得VO中考察少，主要是OA, 而且OA中的运用很多时候是找不到最优解时的暴力解的手段。但是通过pruning是可以加速这个暴力搜索的过程的。DFS题主要是2大类，排列和组合。其中一个我觉得的难点是面对有重复元素的array的排列和组合的去重问题。


### 常见注意事项
DFS问题开始写代码之前，一定要先把问题抽象成一个图的遍历问题。然后再开始做。这样思路会清晰很多，也方便自己逻辑debug      

另外我觉得DFS题自己做的时候内心要问自己下面几个问题：
* 原始输入需要排序吗？
* 要去重吗？
* 形成中的中间态序列是一个array还是string或者是个数字

### 排列
排列题需要一个hashamp叫visited, 记录哪些元素已经被选中了。  排列的个数是n！

[16 · 带重复元素的排列](https://www.lintcode.com/problem/16/)
```python
class Solution:
    """
    @param nums: A list of integers
    @return: A list of unique permutations
    """
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        # write your code here
        self.res = []
        nums.sort()
        vis = [False] *len(nums)
        self.dfs(nums, [],vis)
        return self.res

        
    def dfs(self,nums,comb,visited):
        if len(comb) == len(nums):
            self.res.append(comb[:])
            return
        
        for i in range(len(nums)):
            if visited[i] == True:  continue
            # 去重做法
            if i >= 1 and nums[i-1] == nums[i] and visited[i-1] == False:
                continue
            
            comb.append(nums[i])
            visited[i] = True
            self.dfs(nums,comb,visited)
            visited[i] = False   #要记得把visited给复原
            comb.pop()
```

### 组合
组合思路更简单，组合不考虑顺序，就是考虑每个元素都有选和不选2种结果。所以组合数是2 ^n 个。但是组合的去重需要把原始输入排序以为了让相近的元素挨在一起。

[153 · 数字组合 II](https://www.lintcode.com/problem/153/)
```python
class Solution:
    """
    @param num: Given the candidate numbers
    @param target: Given the target number
    @return: All the combinations that sum to target
    """
    def combinationSum2(self, num, target):
        if not num or len(num) == 0:  return []
        self.res = []
        num.sort()
        self.dfs(num,target,[],num[0],0)
        return self.res

    def dfs(self,candidates,target,comb,curnum,startindx):
        # 这题的非去重输入版是 LC 135
        if target == 0:
            self.res.append(list(comb))
            return 
        if target < 0:
            return

        for i in range(startindx,len(candidates)):
            nextn = candidates[i]
            # 组合的去重做法
            if i > startindx and candidates[i] == candidates[i-1]:
                continue

            comb.append(nextn)
            self.dfs(candidates,target-nextn,comb, nextn,i+1)
            comb.pop()
```




### Nth 排列/组合
有一类题目会问你求第n个排列/组合是多少，或者给定序列的上一个，下一个排列/组合是什么。这类题目前看到有3道。这种问题一般是有更优解，不需要通过DFS去做。但是我觉得可以拿来练手DFS。尤其是练习思考减枝。可以通过减枝实现快速跳到特定的序列。    

#### 相关题目
* [388 · 第k个排列](https://www.lintcode.com/problem/388)
* [51 · 上一个排列](https://www.lintcode.com/problem/51/solution/20325)
* [52 · 下一个排列](https://www.lintcode.com/problem/52)


