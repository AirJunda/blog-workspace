---
title: "Two Pointers复习"
date: 2022-02-01T17:51:43+08:00
draft: false
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

#### Reverse 类
复习一下三步翻转法就好: [旋转数组](https://www.lintcode.com/problem/1334/description)  

图解如下：      
```java
nums = "----->-->"; k =3
result = "-->----->";
reverse "----->-->" we can get "<--<-----"
reverse "<--" we can get "--><-----"
reverse "<-----" we can get "-->----->"
```

#### Two Sum 类
TwoSum原题：给一组整数arr和目标值，是否存在任意一对数字之和等于这个值
原题很简单，但是如果输入有重复元素应该如何处理呢？那么就看这题：
[Two Sum - Unique pairs](https://www.lintcode.com/problem/587)  
```python
def twoSum6(self, nums, target):
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
 

#### Partition 类
Partition类题目都会用到下面这个人质交换模板：
```python
while i <= j:
    # 注意是 i <=j, 不是 i < j
     while i <= j and num[i]属于左侧帮派:
        i += 1
     while i <= j and num[j]属于右侧帮派:
         j -= 1
    if i <= j:
        # 交换人质
         num[i], num[j] =  num[j],  num[i]
        i += 1
        j -= 1
```
尤其注意上面的模板里是用的  <=。为什么是等于呢？因为如果i==j,那么最后一个元素依然需要鉴别是左右哪一边。

* 上面的代码走完后，无论num长度是奇数个还是偶数个，i,j各自都在敌对帮派的第一个位置。
* 人质划分后，是不保证原有的元素顺序的。只是保证同一帮派的在一边。（**not stable**）

相关练习：
* https://www.lintcode.com/problem/49/note
* 

##### Quicksort
虽然也用到了上面的模板，但是不是非左即右，而是采用了pivot里外不是人的划分，下面是quicksort的代码：
```python
def sortIntegers(self, A):
        # Write your code here
        self.quickSort(A, 0, len(A) - 1)
    
    def quickSort(self, A, start, end):
        if start >= end:
            return
        
        left, right = start, end
        pivot = A[(start + end) // 2]

        # key point 2: every time you compare left & right, it should be left <= right not left < right
        while left <= right:
            while left <= right and A[left] < pivot:
                # 注意啊！！是 < pivot 不是 <= pivot
                left += 1
            
            while left <= right and A[right] > pivot:
                right -= 1
            
            if left <= right:
                A[left], A[right] = A[right], A[left]
                left += 1
                right -= 1

        self.quickSort(A, start, right)  # right是左派的前线
        self.quickSort(A, left, end)    # left 是右派的前线
```
**关于3种划分的解释**
* 如果改成  A[left] <= pivot， A[right] > pivot 这种非左即右：        
遇到[2,2,2,2] 会分成 [], [2,2,2,2], 跳不出递归。

* 如果改成 A[left] <= pivot， A[right] >= pivot 这种双面间谍：
遇到[2,2,2,2] 还是会分成 [], [2,2,2,2], 跳不出递归。

* 最后采用的 A[left] < pivot， A[right] > pivot 这种里外不是人的：
遇到等于pivot的元素，2个帮派都把他当对方的人，遇到就拿去交换。那么最后这些散落的，重复的pivot就会随机的在2个帮派之间分摊掉。避免形成 [], 从而避免跳不出递归。

##### QuickSelect
一个o(N)的find kth element的算法。用到了和quicksort一样的分区。思路就是通过快速排序算法的partition步骤，可以将小于pivot的值划分到pivot左边，大于pivot的值划分到pivot右边，所以可以直接得到pivot的rank。从而缩小范围继续找第k大的值。[练习题](https://www.lintcode.com/problem/461/note)

```python
def kthSmallest(self, k, nums):
        n = len(nums)
        return self.partition(nums, 0, n - 1, k - 1)
        
    def partition(self, nums, start, end, k):
        left, right = start, end
        pindex = (left + right) // 2
        pivot = nums[pindex]
        
        while left <= right:
            while left <= right and nums[left] < pivot:
                left += 1
            while left <= right and nums[right] > pivot:
                right -= 1
            if left <= right:
                nums[left], nums[right] = nums[right], nums[left]
                left += 1
                right -= 1
        
        # 如果第 k 小在右侧，搜索右边的范围，否则搜索左侧。
        if k <= right:
            return self.partition(nums, start, right, k)
        if k >= left:
            return self.partition(nums, left, end, k)
        # 走到这，说明k不在左和右，只能是pivot的位置。
        return pivot
```

##### 3个Pointers将array分成3端
这个类型题目少一些。经典题是[Sort Colors](https://www.lintcode.com/problem/148/)：
给定一个包含红，白，蓝且长度为 n 的数组，将数组元素进行分类使相同颜色的元素相邻，并按照红、白、蓝的顺序进行排序。我们使用整数 0，1 和 2 分别代表红，白，蓝

这题的思路是弄3个Pointers：
left之前的(不包括left)都是0，right之后的(不包括right)都是2。i遍历整个array,遇到0就放到左边，遇到1就放到右边，每次放的操作完成后，需要相应的更新left,和right的位置以更新左右2个派系的边界

```python
def sortColors(self, A):
        left, index, right = 0, 0, len(A) - 1
        
        while index <= right:
            if A[index] == 1:
                index += 1
            elif A[index] == 0:
                if index >= left:
                    A[index], A[left] = A[left], A[index]
                index += 1
                left += 1
            elif A[index] == 2:
                A[index], A[right] = A[right], A[index]
                right -= 1

```
Follow-Up: [当有K个颜色时，怎么做？](https://www.lintcode.com/problem/143/)


 ### 同向双指针
 同向双指针这块需要未来慢慢补充。目前看下下面这题。

  上一节的交换人质模板适用的题里，如果题目提到要保留原有顺序，那交换人质就不能用了。就需要用同向双针来做。例题就是[Move Zeros](https://www.lintcode.com/problem/539/)









 
