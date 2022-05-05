# 题目

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回子数组内所有元素的乘积严格小于 `k` 的连续子数组的数目。

**示例 1：**

```
输入：nums = [10,5,2,6], k = 100
输出：8
解释：8 个乘积小于 100 的子数组分别为：[10]、[5]、[2],、[6]、[10,5]、[5,2]、[2,6]、[5,2,6]。
需要注意的是 [10,5,2] 并不是乘积小于 100 的子数组。
```

**示例 2：**

```
输入：nums = [1,2,3], k = 0
输出：0
```

**提示:** 

- $1 <= nums.length <= 3 * 10^4$
- `1 <= nums[i] <= 1000`
- $0 <= k <= 10^6$

## 我的解法

未解出，想到过动态规划，但是不知道如何处理子数组！

## 其他解法

### 二分查找

```c++
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        if (k == 0) {
            return 0;
        }
        int n = nums.size();
        vector<double> logPrefix(n + 1);
        for (int i = 0; i < n; i++) {
            logPrefix[i + 1] = logPrefix[i] + log(nums[i]);
        }
        double logk = log(k);
        int ret = 0;
        for (int j = 0; j < n; j++) {
            int l = upper_bound(logPrefix.begin(), logPrefix.begin() + j + 1, logPrefix[j + 1] - log(k) + 1e-10) - logPrefix.begin();
            ret += j + 1 - l;
        }
        return ret;
    }
};
```

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k == 0) {
            return 0;
        }
        int n = nums.length;
        double[] logPrefix = new double[n + 1];
        for (int i = 0; i < n; i++) {
            logPrefix[i + 1] = logPrefix[i] + Math.log(nums[i]);
        }
        double logk = Math.log(k);
        int ret = 0;
        for (int j = 0; j < n; j++) {
            int l = 0;
            int r = j + 1;
            int idx = j + 1;
            double val = logPrefix[j + 1] - logk + 1e-10;
            while (l <= r) {
                int mid = (l + r) / 2;
                if (logPrefix[mid] > val) {
                    idx = mid;
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }
            ret += j + 1 - idx;
        }
        return ret;
    }
}
```

```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        if k == 0:
            return 0
        ans, n = 0, len(nums)
        logPrefix = [0] * (n + 1)
        for i, num in enumerate(nums):
            logPrefix[i + 1] = logPrefix[i] + log(num)
        logK = log(k)
        for j in range(1, n + 1):
            l = bisect_right(logPrefix, logPrefix[j] - logK + 1e-10, 0, j)
            ans += j - l
        return ans
```

#### 思路与算法

子数组 $[i, j]$ 的元素乘积小于 k，即 $\prod_{l=i}^{j} \textit{nums}[l] \lt k$

- k = 0。

  由于元素均为正数，所有子数组乘积均大于 0，因此乘积小于 0 的子数组的数目为 0。

- k > 0

  我们在计算子数组 $[i, j]$ 的元素乘积 $\prod_{l=i}^{j} \textit{nums}[l]$ 时，会出现整型溢出的情况。为了避免整型溢出，我们将不等式两边取对数得 $\log \prod_{l=i}^{j} \textit{nums}[l] = \sum_{l=i}^{j} \log \textit{nums}[l] \lt \log k$l，因此「子数组 $[i, j]$ 的元素乘积小于 k」等价于「子数组 [i, j] 的元素对数和小于 $\log k$」。

  我们预处理出数组的元素对数前缀和 $\textit{logPrefix}$，即 $\textit{logPrefix}[i + 1] = \sum_{l=0}^{i} \log \textit{nums}[l]$。因为 $\textit{nums}$是正整数数组，所以 $\textit{logPrefix}$是非递减的。

  枚举子数组的右端点 j，在 $\textit{logPrefix}$的区间 $[0, j]$ 内二分查找满足 $\textit{logPrefix}[j + 1] - \textit{logPrefix}[l] \lt \log k$ 即 $\textit{logPrefix}[l] \gt \textit{logPrefix}[j + 1] - \log k$ 的最小下标 l，那么以 j 为右端点且元素乘积小于 k 的子数组数目为 j + 1 - l。返回所有数目之和。


> $\texttt{double}$类型只能保证 15 位有效数字是精确的。为了避免计算带来的误差，我们将不等式 $\textit{logPrefix}[l] \gt \textit{logPrefix}[j + 1] - \log k$ 的右边加上 $10^{-10}$（题目中的 $\texttt{double}$数值整数部分的数字不超过 5 个），即 $\textit{logPrefix}[l] \gt \textit{logPrefix}[j + 1] - \log k + 10^{-10}$，从而防止不等式两边数值相等却被判定为大于的情况。

#### 复杂度分析

- 时间复杂度：$O(n \log n)$，其中 n 是数组 $\textit{nums}$的长度。预处理数组 $\textit{logPrefix}$需要 O(n)，枚举加二分查找需要 $O(n \log n)$。

- 空间复杂度：O(n)。保存数组 $\textit{logPrefix}$需要 O(n) 的空间。


### 滑动窗口

```c++
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        int n = nums.size(), ret = 0;
        int prod = 1, i = 0;
        for (int j = 0; j < n; j++) {
            prod *= nums[j];
            while (i <= j && prod >= k) {
                prod /= nums[i];
                i++;
            }
            ret += j - i + 1;
        }
        return ret;
    }
};
```

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        int n = nums.length, ret = 0;
        int prod = 1, i = 0;
        for (int j = 0; j < n; j++) {
            prod *= nums[j];
            while (i <= j && prod >= k) {
                prod /= nums[i];
                i++;
            }
            ret += j - i + 1;
        }
        return ret;
    }
}
```

```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        ans, prod, i = 0, 1, 0
        for j, num in enumerate(nums):
            prod *= num
            while i <= j and prod >= k:
                prod //= nums[i]
                i += 1
            ans += j - i + 1
        return ans
```

#### 思路与算法

我们固定子数组 $[i, j]$ 的右端点 j 时，显然左端点 i 越大，子数组元素乘积越小。对于子数组 $[i, j]$，当左端点 $i \ge l_1$时，所有子数组的元素乘积都小于 k，当左端点 $i \lt l_1$时，所有子数组的元素乘积都大于等于 k。那么对于右端点为 j + 1 的所有子数组，它的左端点 i 就不需要从 0 开始枚举，因为对于所有 $i \lt l_1$的子数组，它们的元素乘积都大于等于 k。我们只要从 $i = l_1$处开始枚举，直到子数组 $i = l_2$时子数组 $[l_2, j + 1] $的元素乘积小于 k，那么左端点 $i \ge l_2$所有子数组的元素乘积都小于 k。

根据上面的分析，我们枚举子数组的右端点 j，并且左端点从 i = 0 开始，用 $\textit{prod}$记录子数组 $[i, j]$ 的元素乘积。每枚举一个右端点 j，如果当前子数组元素乘积 $\textit{prod}$大于等于 k，那么我们右移左端点 i 直到满足当前子数组元素乘积小于 k 或者 i > j，那么元素乘积小于 k 的子数组数目为 j - i + 1。返回所有数目之和。

> $\textit{prod}$的值始终不超过 $k \times \max_l \{\textit{nums}[l] \}$，因此无需担心整型溢出的问题。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 $\textit{nums}$的长度。两个端点 i 和 j 的增加次数都不超过 n。

- 空间复杂度：O(1)。


