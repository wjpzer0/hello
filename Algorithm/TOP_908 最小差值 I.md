# 题目

给你一个整数数组 `nums`，和一个整数 `k` 。

在一个操作中，您可以选择 `0 <= i < nums.length` 的任何索引 `i` 。将 `nums[i]` 改为 `nums[i] + x` ，其中 `x` 是一个范围为 `[-k, k]` 的整数。对于每个索引 `i` ，最多 **只能** 应用 **一次** 此操作。

`nums` 的 **分数** 是 `nums` 中最大和最小元素的差值。 

*在对 `nums` 中的每个索引最多应用一次上述操作后，返回 `nums` 的最低 **分数*** 。

**示例 1：**

```
输入：nums = [1], k = 0
输出：0
解释：分数是 max(nums) - min(nums) = 1 - 1 = 0。
```

**示例 2：**

```
输入：nums = [0,10], k = 2
输出：6
解释：将 nums 改为 [2,8]。分数是 max(nums) - min(nums) = 8 - 2 = 6。
```

**示例 3：**

```
输入：nums = [1,3,6], k = 3
输出：0
解释：将 nums 改为 [4,4,4]。分数是 max(nums) - min(nums) = 4 - 4 = 0。
```

**提示：**

- $1 <= nums.length <= 10^4$
- $0 <= nums[i] <= 10^4$
- $0 <= k <= 10^4$

## 我的解法

思路简单明了，游刃有余！

```python
class Solution:
    def smallestRangeI(self, nums: List[int], k: int) -> int:
        maxNum = max(nums)
        minNum = min(nums)
        ans = max(maxNum - minNum - 2 * k, 0)
        return ans
```

## 其他解法

### 数学

```c++
class Solution {
public:
    int smallestRangeI(vector<int>& nums, int k) {
        int minNum = *min_element(nums.begin(), nums.end());
        int maxNum = *max_element(nums.begin(), nums.end());
        return maxNum - minNum <= 2 * k ? 0 : maxNum - minNum - 2 * k;
    }
};
```

```java
class Solution {
    public int smallestRangeI(int[] nums, int k) {
        int minNum = Arrays.stream(nums).min().getAsInt();
        int maxNum = Arrays.stream(nums).max().getAsInt();
        return maxNum - minNum <= 2 * k ? 0 : maxNum - minNum - 2 * k;
    }
}
```

```python
class Solution:
    def smallestRangeI(self, nums: List[int], k: int) -> int:
        return max(0, max(nums) - min(nums) - 2 * k)
```

#### 思路与算法

假设整数数组 $\textit{nums}$的最小值为 $\textit{minNum}$，最大值为$ \textit{maxNum}$。

- 如果 $\textit{maxNum} - \textit{minNum} \le 2k$，那么我们总可以将整数数组 $\textit{nums}$的所有元素都改为同一个整数，因此更改后的整数数组 $\textit{nums}$的最低分数为 0。

  > 证明：因为 $\textit{maxNum} - \textit{minNum} \le 2k$，所以存在整数 $x \in [\textit{minNum}, \textit{maxNum}]$，使得 $x - \textit{minNum} \le k$ 且 $\textit{maxNum} - x \le k$。那么整数数组 $\textit{nums}$的所有元素与整数 x 的绝对差值都不超过 k，即所有元素都可以改为 x。

- 如果 $\textit{maxNum} - \textit{minNum} \gt 2k$，那么更改后的整数数组 $\textit{nums}$的最低分数为 $\textit{maxNum} - \textit{minNum} - 2k$。

  > 证明：对于 $\textit{minNum}$和 $\textit{maxNum}$两个元素，我们将 $\textit{minNum}$改为 $\textit{minNum} + k$，$\textit{maxNum}$改为 $\textit{maxNum} - k$，此时两个元素的绝对差值最小。因此更改后的整数数组 $\textit{nums}$的最低分数大于等于$ \textit{maxNum} - \textit{minNum} - 2k$。
  > 对于整数数组 $\textit{nums}$中的元素 x，如果 $x \lt \textit{minNum} + k$，那么 x 可以更改为 $\textit{minNum} + k$，如果 $x \gt \textit{maxNum} - k$，那么 x 可以更改为 $\textit{maxNum} - k$，因此整数数组 $\textit{nums}$的所有元素都可以改为区间$ [\textit{minNum} + k, \textit{maxNum} - k]$ 的整数，所以更改后的整数数组 $\textit{nums}$的最低分数小于等于 $\textit{maxNum} - \textit{minNum} - 2k$。
  > 综上所述，更改后的整数数组 $\textit{nums}$的最低分数为 $\textit{maxNum} - \textit{minNum} - 2k$。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是整数数组 $\textit{nums}$的长度。需要 O(n) 的时间遍历数组 $\textit{nums}$得到最小值和最大值，然后需要 O(1) 的时间计算最低分数。

- 空间复杂度：O(1)。


