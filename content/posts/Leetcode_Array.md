+++
title = 'Leetcode Array'
date = 2024-01-07T17:57:04+08:00
draft = false
math = true
tags = ["algorithm", "leetcode"]
+++

# Leetcode Array

## 目录
- [二分](#二分)
    - [收敛位置证明 leetcode35](#收敛位置证明-leetcode35)
- 


## 二分

**Template**
```python
# 左闭右开
def search(self, nums: List[int], target: int) -> int:
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > target:
            right = mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            return mid
    return -1

# 左闭右闭
def search(self, nums: List[int], target: int) -> int:
    left , right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] > target:
            right = mid - 1
        elif nums[mid] < target:
            left = mid + 1
        else:
            return mid
    return -1
```
### 收敛位置证明 leetcode35
[leetcode35.搜索插入位置](https://leetcode.cn/problems/search-insert-position/description/)。针对该问题，在搜索失败时收敛到哪个位置值得探讨。

**证明问题** 递增序列，采用**左闭右开**二分搜索，如果没有搜索到`target`，`right`的位置为`target`应该插入的位置。（其中`left，right`分别为左右区间下标；`target`为目标值，`nums`为序列）

**证明过程** 
为了证明方便，首先我们初始化`nums[right] == inf`，在证明结论二，三中使用到。
1. 结论一，在目标搜索不到情况下，退出循环的时候，`left == right`。假设退出循环`left > right`，则只可能通过上一步`left = mid+1`，则需要'mid >= right'。但事实上由于`mid = (left + right) // 2`，并且进入循环`left < right`，所以`mid < right`，与`mid >= right`矛盾，所以假设不成立。
2. 结论二，在收敛过程中，`nums[right] > target`永远满足，已知一开始`nums[right] > target`，right在收敛过程中，使用表达式`right = mid`更新，且更新条件为`nums[mid] > target`，所以`nums[right] > target`永远满足。
3. 结论三，在目标搜索不到情况下，`nums[right]`为第一个大于`target`的数。假设在`right`前面还存在比`nums[right]`小，但是大于`target`的数，则`nums[right-1] > target`。根据结论一，可知退出循环的时候`left == right`， 则在上一轮循环，执行了`left(right) = mid + 1 -> mid = right - 1` ，且执行条件为`nums[mid] < target -> nums[right-1] < target`，与假设相矛盾，故假设不成立。
4. 结论四，在目标搜索不到情况下，退出循环的时候，right的位置为target应该插入的位置。根据结论二和结论三，可得结论四成立。
