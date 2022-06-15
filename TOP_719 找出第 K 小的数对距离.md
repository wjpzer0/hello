# 题目

数对 `(a,b)` 由整数 `a` 和 `b` 组成，其数对距离定义为 `a` 和 `b` 的绝对差值。

给你一个整数数组 `nums` 和一个整数 `k` ，数对由 `nums[i]` 和 `nums[j]` 组成且满足 `0 <= i < j < nums.length` 。返回 **所有数对距离中** 第 `k` 小的数对距离。

**示例 1：**

```
输入：nums = [1,3,1], k = 1
输出：0
解释：数对和对应的距离如下：
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
距离第 1 小的数对是 (1,1) ，距离为 0 。
```

**示例 2：**

```
输入：nums = [1,1,1], k = 2
输出：0
```

**示例 3：**

```
输入：nums = [1,6,1], k = 3
输出：5
```

**提示：**

- `n == nums.length`
- `2 <= n <= 104`
- `0 <= nums[i] <= 106`
- `1 <= k <= n * (n - 1) / 2`

## 我的解法

未解出，不知道如何利用二分查找解决题目！

## 其他解法

### 排序 + 二分查找

```c++
class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int n = nums.size(), left = 0, right = nums.back() - nums.front();
        while (left <= right) {
            int mid = (left + right) / 2;
            int cnt = 0;
            for (int j = 0; j < n; j++) {
                int i = lower_bound(nums.begin(), nums.begin() + j, nums[j] - mid) - nums.begin();
                cnt += j - i;
            }
            if (cnt >= k) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```

```java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length, left = 0, right = nums[n - 1] - nums[0];
        while (left <= right) {
            int mid = (left + right) / 2;
            int cnt = 0;
            for (int j = 0; j < n; j++) {
                int i = binarySearch(nums, j, nums[j] - mid);
                cnt += j - i;
            }
            if (cnt >= k) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    public int binarySearch(int[] nums, int end, int target) {
        int left = 0, right = end;
        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
}
```

```python
class Solution:
    def smallestDistancePair(self, nums: List[int], k: int) -> int:
        def count(mid: int) -> int:
            cnt = 0
            for j, num in enumerate(nums):
                i = bisect_left(nums, num - mid, 0, j)
                cnt += j - i
            return cnt

        nums.sort()
        return bisect_left(range(nums[-1] - nums[0]), k, key=count)
```

先将数组 $\textit{nums}$从小到大进行排序。因为第 k 小的数对距离必然在区间$ [0, \max (\textit{nums}) - \min(\textit{nums})]$ 内，令 $\textit{left} = 0$，$\textit{right} = \max (\textit{nums}) - \min(\textit{nums})$，我们在区间 $[\textit{left}, \textit{right}]$上进行二分。

对于当前搜索的距离 $\textit{mid}$，计算所有距离小于等于 $\textit{mid}$的数对数目 $\textit{cnt}$，如果 $\textit{cnt} \ge k$，那么 $\textit{right} = \textit{mid} - 1$，否则 $\textit{left} = \textit{mid} + 1$。当 $\textit{left} \gt \textit{right}$ 时，终止搜索，那么第 k 小的数对距离为 $\textit{left}$。

给定距离 $\textit{mid}$，计算所有距离小于等于 $\textit{mid}$的数对数目 $\textit{cnt}$可以使用二分查找：枚举所有数对的右端点 j，二分查找大于等于 $\textit{nums}[j] - \textit{mid}$ 的最小值的下标 i，那么右端点为 j 且距离小于等于 $\textit{mid}$的数对数目为 j - i，计算这些数目之和。

#### 复杂度分析

- 时间复杂度：$O(n \log n \times \log D)$，其中 n 是数组 $\textit{nums}$的长度，$D = \max(\textit{nums}) - \min(\textit{nums})$。外层二分查找需要 $O(\log D)$，内层二分查找需要 $O(n \log n)$。

- 空间复杂度：$O(\log n)$。排序的平均空间复杂度为 $O(\log n)$。


### 排序 + 二分查找 + 双指针

```c++
class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int n = nums.size(), left = 0, right = nums.back() - nums.front();
        while (left <= right) {
            int mid = (left + right) / 2;
            int cnt = 0;
            for (int i = 0, j = 0; j < n; j++) {
                while (nums[j] - nums[i] > mid) {
                    i++;
                }
                cnt += j - i;
            }
            if (cnt >= k) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```

```java
class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length, left = 0, right = nums[n - 1] - nums[0];
        while (left <= right) {
            int mid = (left + right) / 2;
            int cnt = 0;
            for (int i = 0, j = 0; j < n; j++) {
                while (nums[j] - nums[i] > mid) {
                    i++;
                }
                cnt += j - i;
            }
            if (cnt >= k) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
}
```

```python
class Solution:
    def smallestDistancePair(self, nums: List[int], k: int) -> int:
        def count(mid: int) -> int:
            cnt = i = 0
            for j, num in enumerate(nums):
                while num - nums[i] > mid:
                    i += 1
                cnt += j - i
            return cnt

        nums.sort()
        return bisect_left(range(nums[-1] - nums[0]), k, key=count)
```

给定距离 $\textit{mid}$，计算所有距离小于等于 $\textit{mid}$的数对数目$ \textit{cnt}$可以使用双指针：初始左端点 i = 0，我们从小到大枚举所有数对的右端点 j，移动左端点直到 $\textit{nums}[j] - \textit{nums}[i] \le \textit{mid}$，那么右端点为 j 且距离小于等于 $\textit{mid}$的数对数目为 j - i，计算这些数目之和。

#### 复杂度分析

- 时间复杂度：$O(n \times (\log n + \log D))$O(n×(logn+logD))，其中 n 是数组 $\textit{nums}$的长度，D = $\max(\textit{nums}) - \min(\textit{nums})$。外层二分查找需要 O(\log D)，内层双指针需要 O(n)，排序的平均时间复杂度为 $O(n \log n)$。

- 空间复杂度：$O(\log n)$。排序的平均空间复杂度为 $O(\log n)$。


