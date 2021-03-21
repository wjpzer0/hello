# 题目

升序排列的整数数组 nums 在预先未知的某个点上进行了旋转（例如， [0,1,2,4,5,6,7] 经旋转后可能变为 [4,5,6,7,0,1,2] ）。

请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

示例 1：

```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```


示例 2：

```
输入：nums = [4,5,6,7,0,1,2], target = 3
输出：-1
```


示例 3：

```
输入：nums = [1], target = 0
输出：-1
```


提示：

- 1 <= nums.length <= 5000
- -10^4 <= nums[i] <= 10^4
- nums 中的每个值都 独一无二
- nums 肯定会在某个点上旋转
- -10^4 <= target <= 10^4

## 我的解法

恩未读懂题目，要求时间复杂度为O(log(n)),未达成

两种不符合题意的解法

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        i = 0
        while i < len(nums):
            if target == nums[i]:
                return i
            i += 1
        else:
            return -1
```



```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def isPivot(nums):
            i = 0
            while i + 1 < len(nums):
                if nums[i + 1] < nums[i]:
                    return i + 1
                i += 1
            return i
        Pivot = isPivot(nums)
        if target < nums[0]:
            i = Pivot
            while i < len(nums):
                if target == nums[i]:
                    return i
                i += 1
            else:
                return -1
        else:
            i = 0
            while i <= Pivot:
                if target == nums[i]:
                    return i
                i += 1
            else:
                return -1
```

## 其他解法

### 二分搜索

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        if not nums:
            return -1
        l, r = 0, len(nums) - 1
        while l <= r:
            mid = (l + r) // 2
            if nums[mid] == target:
                return mid
            if nums[0] <= nums[mid]:
                if nums[0] <= target < nums[mid]:
                    r = mid - 1
                else:
                    l = mid + 1
            else:
                if nums[mid] < target <= nums[len(nums) - 1]:
                    l = mid + 1
                else:
                    r = mid - 1
        return -1
```



#### 思路和算法

题目要求算法时间复杂度必须是 O(logn) 的级别，这提示我们可以使用二分搜索的方法。

但是数组本身不是有序的，进行旋转后只保证了数组的局部是有序的，这还能进行二分搜索吗？答案是可以的。

可以发现的是，我们将数组从中间分开成左右两部分的时候，一定有一部分的数组是有序的。拿示例来看，我们从 6 这个位置分开以后数组变成了 [4, 5, 6] 和 [7, 0, 1, 2] 两个部分，其中左边 [4, 5, 6] 这个部分的数组是有序的，其他也是如此。

这启示我们可以在常规二分搜索的时候查看当前 mid 为分割位置分割出来的两个部分 [l, mid] 和 [mid + 1, r] 哪个部分是有序的，并根据有序的那个部分确定我们该如何改变二分搜索的上下界，因为我们能够根据有序的那部分判断出 target 在不在这个部分：

- 如果 [l, mid - 1] 是有序数组，且 target 的大小满足 [*nums*[*l*],*nums*[*m**i**d*])，则我们应该将搜索范围缩小至 [l, mid - 1]，否则在 [mid + 1, r] 中寻找。

- 如果 [mid, r] 是有序数组，且 target 的大小满足 (*nums*[*m**i**d*+1],*nums*[*r*]]，则我们应该将搜索范围缩小至 [mid + 1, r]，否则在 [l, mid - 1] 中寻找。

  ![fig1](https://assets.leetcode-cn.com/solution-static/33/33_fig1.png)


需要注意的是，二分的写法有很多种，所以在判断 target 大小与有序部分的关系的时候可能会出现细节上的差别。

#### 复杂度分析

- 时间复杂度： O(logn)，其中 n 为 nums[] 数组的大小。整个算法时间复杂度即为二分搜索的时间复杂度 O(logn)。

- 空间复杂度： O(1) 。我们只需要常数级别的空间存放变量。


