# 题目

统计一个数字在排序数组中出现的次数。

**示例 1:**

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

**示例 2:**

```
输入: nums = [5,7,7,8,8,10], target = 6
输出: 0
```

**限制：**

```
0 <= 数组长度 <= 50000
```



**注意：**本题与主站 34 题相同（仅返回值不同）：https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

## 我的解法

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        idx = bisect.bisect_left(nums, target)
        # print(idx)
        res = 0
        l = len(nums)
        for i in range(idx, l):
            if nums[i] == target:
                res += 1
            else:
                break
        return res
```

恩，利用了内置函数，没有自己写二分查找！

## 其他解法

### 二分查找

```c++
class Solution {
public:
    int binarySearch(vector<int>& nums, int target, bool lower) {
        int left = 0, right = (int)nums.size() - 1, ans = (int)nums.size();
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

    int search(vector<int>& nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.size() && nums[leftIdx] == target && nums[rightIdx] == target) {
            return rightIdx - leftIdx + 1;
        }
        return 0;
    }
};
```

```java
class Solution {
    public int search(int[] nums, int target) {
        int leftIdx = binarySearch(nums, target, true);
        int rightIdx = binarySearch(nums, target, false) - 1;
        if (leftIdx <= rightIdx && rightIdx < nums.length && nums[leftIdx] == target && nums[rightIdx] == target) {
            return rightIdx - leftIdx + 1;
        } 
        return 0;
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

直观的思路肯定是从前往后遍历一遍。用两个变量记录第一次和最后一次遇见 $\textit{target}$ 的下标，但这个方法的时间复杂度为 O(n)，没有利用到数组升序排列的条件。

由于数组已经排序，因此整个数组是单调递增的，我们可以利用二分法来加速查找的过程。

考虑 $\textit{target}$ 在数组中出现的次数，其实我们要找的就是数组中「第一个等于 $\textit{target}$ 的位置」（记为 $\textit{leftIdx}$）和「第一个大于 $\textit{target}$ 的位置减一」（记为 $\textit{rightIdx}$）。当 $\textit{target}$ 在数组中存在时，$\textit{target}$ 在数组中出现的次数为 $\textit{rightIdx}-\textit{leftIdx}+1$。

二分查找中，寻找 $\textit{leftIdx}$ 即为在数组中寻找第一个大于等于 $\textit{target}$ 的下标，寻找 $\textit{rightIdx}$ 即为在数组中寻找第一个大于 $\textit{target}$ 的下标，然后将下标减一。两者的判断条件不同，为了代码的复用，我们定义 binarySearch(nums, target, lower) 表示在 $\textit{nums}$ 数组中二分查找 $\textit{target}$ 的位置，如果 $\textit{lower}$ 为 $\rm true$，则查找第一个大于等于 $\textit{target}$ 的下标，否则查找第一个大于 $\textit{target}$ 的下标。

最后，因为 $\textit{target}$ 可能不存在数组中，因此我们需要重新校验我们得到的两个下标 $\textit{leftIdx}$ 和 $\textit{rightIdx}$，看是否符合条件，如果符合条件就返回 $\textit{rightIdx}-\textit{leftIdx}+1$，不符合就返回 0。

#### 复杂度分析

- 时间复杂度： $O(\log n)$ ，其中 n 为数组的长度。二分查找的时间复杂度为 $O(\log n)$，一共会执行两次，因此总时间复杂度为 $O(\log n)$。

- 空间复杂度：O(1)。只需要常数空间存放若干变量。


