# 题目

给你一个整数数组 `nums`，将它重新排列成 `nums[0] < nums[1] > nums[2] < nums[3]...` 的顺序。

你可以假设所有输入数组都可以得到满足题目要求的结果。

**示例 1：**

```
输入：nums = [1,5,1,1,6,4]
输出：[1,6,1,5,1,4]
解释：[1,4,1,5,1,6] 同样是符合题目要求的结果，可以被判题程序接受。
```

**示例 2：**

```
输入：nums = [1,3,2,2,3,1]
输出：[2,3,1,3,1,2]
```

**提示：**

- $1 <= nums.length <= 5 * 10^4$
- `0 <= nums[i] <= 5000`
- 题目数据保证，对于给定的输入 `nums` ，总能产生满足题目要求的结果

**进阶：**你能用 O(n) 时间复杂度和 / 或原地 O(1) 额外空间来实现吗？

## 我的解法

查看题解后处理了边界条件后通过！进阶写不出来！

```python
class Solution:
    def wiggleSort(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        numsNew = nums[:]
        numsNew.sort()
        n = len(nums)
        i = (n - 1)  // 2
        j = n - 1
        for point in range(n):
            if point % 2 == 0:
                nums[point] = numsNew[i]
                i -= 1
            else:
                nums[point] = numsNew[j]
                j -= 1
```

## 其他解法

[摆动排序 II - 摆动排序 II - 力扣（LeetCode）](https://leetcode.cn/problems/wiggle-sort-ii/solution/bai-dong-pai-xu-ii-by-leetcode-solution-no0s/)