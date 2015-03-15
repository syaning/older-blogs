title: 字符串及数组练习题（一）
date: 2014-09-10 22:23:03
categories: DS&Algorithm
tags:
---
### 1. 在一个字符串中找到第一个只出现一次的字符
如果针对每个字符都遍历整个字符串，那么时间复杂度为O(n^2)，因此好的做法是第一次遍历字符串，存成哈希表，第二次遍历字符串，找到第一个只出现一次的字符。
```python
def firstSingleChar(s):
    dic = {}
    for c in s:
        v = dic.get(c)
        dic[c] = v + 1 if v else 1
    
    for c in s:
        v = dic.get(c)
        if v == 1:
            return c
    
    return None
```
<!-- more -->
### 2. 求数组的最大子序列和
方法一代码如下，时间复杂度为O(n^2),
```python
def maxSum(lst):
    maxSum = 0
    for i in range(0, len(lst)):
        curSum = 0
        for j in range(i, len(lst)):
            curSum += lst[j]
            if curSum > maxSum:
                maxSum = curSum
    return maxSum
```

方法二进行了优化，时间复杂度为O(n)，即只需要遍历一遍数组：
```python
def maxSum(lst):
    maxSum, curSum = 0, 0
    for i in lst:
        curSum += i
        if curSum > maxSum:
            maxSum = curSum
        elif curSum < 0:
            curSum = 0
    return maxSum
```



### 参考文章
[[分类整理IV]微软等100题系列V0.1版：字符串+数组面试题集锦](http://blog.csdn.net/v_july_v/article/details/6106226)
[在一个字符串中找到第一个只出现一次的字符](http://www.cnblogs.com/liuyubloch/archive/2012/08/25/2655705.html)
[最大子序列和问题](http://www.cnblogs.com/CCBB/archive/2009/04/25/1443455.html)