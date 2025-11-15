# 题目

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

进阶：

你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？


示例 1：

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```


示例 2：

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```


示例 3：

```
输入：nums = [], target = 0
输出：[-1,-1]
```


提示：

- 0 <= nums.length <= 105
- -109 <= nums[i] <= 109
- nums 是一个非递减数组
- -109 <= target <= 109

## 我的解法

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        numsLen = len(nums)
        point = 0
        amount = 0
        start = -1
        isBe = False
        limits = [-1, -1]
        while point < numsLen:
            if nums[point] == target:
                isBe = True
                if amount == 0:
                    start = point
                    amount += 1
                else:
                    amount += 1
            point += 1
        if isBe:
            limits[0] = start
            limits[1] = start + amount -1     
        return limits
```

进阶，想到了二分差找，但无法写出来，对二分查找不熟练。

## 其他解法

### 二分查找

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.length && nums[leftIdx] == target && nums[rightIdx] == target) {
            return new int[]{leftIdx, rightIdx};
        } 
        return new int[]{-1, -1};
    }

    public int binarySearch(int[] nums, int target, boolean lower) {
        int left = 0, right = nums.length - 1, ans = nums.length;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (lower && nums[mid] >= target)) {
                right = mid - 1;
                ans = mid;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}
```

```python
class Solution(object):
    def searchRange(self,nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        def left_func(nums,target):
            n = len(nums)-1
            left = 0
            right = n
            while(left<=right):
                mid = (left+right)//2
                if nums[mid] >= target:
                    right = mid-1
                if nums[mid] < target:
                    left = mid+1
            return left
        a =  left_func(nums,target)
        b = left_func(nums,target+1)
        if  a == len(nums) or nums[a] != target:
            return [-1,-1]
        else:
            return [a,b-1]
```

![img](https://assets.leetcode-cn.com/solution-static/34/1.png)
![img](https://assets.leetcode-cn.com/solution-static/34/2.png)
![img](https://assets.leetcode-cn.com/solution-static/34/3.png)
![img](https://assets.leetcode-cn.com/solution-static/34/4.png)
![img](https://assets.leetcode-cn.com/solution-static/34/5.png)
![img](https://assets.leetcode-cn.com/solution-static/34/6.png)
![img](https://assets.leetcode-cn.com/solution-static/34/7.png)
![img](https://assets.leetcode-cn.com/solution-static/34/8.png)
![img](https://assets.leetcode-cn.com/solution-static/34/9.png)
![img](https://assets.leetcode-cn.com/solution-static/34/10.png)
![img](https://assets.leetcode-cn.com/solution-static/34/11.png)
![img](https://assets.leetcode-cn.com/solution-static/34/12.png)
![img](https://assets.leetcode-cn.com/solution-static/34/13.png)
![img](https://assets.leetcode-cn.com/solution-static/34/14.png)

直观的思路肯定是从前往后遍历一遍。用两个变量记录第一次和最后一次遇见target 的下标，但这个方法的时间复杂度为 O(n)，没有利用到数组升序排列的条件。

由于数组已经排序，因此整个数组是单调递增的，我们可以利用二分法来加速查找的过程。

考虑 target 开始和结束位置，其实我们要找的就是数组中「第一个等于 target 的位置」（记为 leftIdx）和「第一个大于 target 的位置减一」（记为rightIdx）。

二分查找中，寻找 leftIdx 即为在数组中寻找第一个大于等于 target 的下标，寻找 rightIdx 即为在数组中寻找第一个大于 target 的下标，然后将下标减一。两者的判断条件不同，为了代码的复用，我们定义 binarySearch(nums, target, lower) 表示在 nums 数组中二分查找 target 的位置，如果 lower 为 true，则查找第一个大于等于 target 的下标，否则查找第一个大于 target 的下标。

最后，因为 target 可能不存在数组中，因此我们需要重新校验我们得到的两个下标 leftIdx 和 rightIdx，看是否符合条件，如果符合条件就返回 [leftIdx,rightIdx]，不符合就返回 [−1,−1]。

#### 复杂度分析

时间复杂度： O(logn) ，其中 n 为数组的长度。二分查找的时间复杂度为 O(logn)，一共会执行两次，因此总时间复杂度为 O(logn)。

空间复杂度：O(1) 。只需要常数空间存放若干变量。

