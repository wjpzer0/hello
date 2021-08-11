# 题目

给你一个整数数组 `nums` ，返回 `nums` 中所有 **等差子序列** 的数目。

如果一个序列中 **至少有三个元素** ，并且任意两个相邻元素之差相同，则称该序列为等差序列。

- 例如，`[1, 3, 5, 7, 9]`、`[7, 7, 7, 7]` 和 `[3, -1, -5, -9]` 都是等差序列。
- 再例如，`[1, 1, 2, 5, 7]` 不是等差序列。

数组中的子序列是从数组中删除一些元素（也可能不删除）得到的一个序列。

- 例如，`[2,5,10]` 是 [1,2,1,***2***,4,1,***5***,***10***] 的一个子序列。

题目数据保证答案是一个 **32-bit** 整数。 

**示例 1：**

```
输入：nums = [2,4,6,8,10]
输出：7
解释：所有的等差子序列为：
[2,4,6]
[4,6,8]
[6,8,10]
[2,4,6,8]
[4,6,8,10]
[2,4,6,8,10]
[2,6,10]
```

**示例 2：**

```
输入：nums = [7,7,7,7,7]
输出：16
解释：数组中的任意子序列都是等差子序列。
```

**提示：**

- `1 <= nums.length <= 1000`
- $-2^{31} <= nums[i] <= 2^{31} - 1$​

## 我的解法

未解出，想到了动态规划，并建立了矩阵，但是无法解决状态转移的方程！

## 其他解法

### 动态规划 + 哈希表

```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int> &nums) {
        int ans = 0;
        int n = nums.size();
        vector<unordered_map<long long, int>> f(n);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                long long d = 1LL * nums[i] - nums[j];
                auto it = f[j].find(d);
                int cnt = it == f[j].end() ? 0 : it->second;
                ans += cnt;
                f[i][d] += cnt + 1;
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        int ans = 0;
        int n = nums.length;
        Map<Long, Integer>[] f = new Map[n];
        for (int i = 0; i < n; ++i) {
            f[i] = new HashMap<Long, Integer>();
        }
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                long d = 1L * nums[i] - nums[j];
                int cnt = f[j].getOrDefault(d, 0);
                ans += cnt;
                f[i].put(d, f[i].getOrDefault(d, 0) + cnt + 1);
            }
        }
        return ans;
    }
}
```

```python
class Solution:
    def numberOfArithmeticSlices(self, nums: List[int]) -> int:
        ans = 0
        f = [defaultdict(int) for _ in nums]
        for i, x in enumerate(nums):
            for j in range(i):
                d = x - nums[j]
                cnt = f[j][d]
                ans += cnt
                f[i][d] += cnt + 1
        return ans
```

我们首先考虑至少有两个元素的等差子序列，下文将其称作弱等差子序列。

由于尾项和公差可以确定一个等差数列，因此我们定义状态 $f[i][d]$​​ 表示尾项为 $\textit{nums}[i]$​，公差为 d 的弱等差子序列的个数。

我们用一个二重循环去遍历 $\textit{nums}$​ 中的所有元素对 $(\textit{nums}[i],\textit{nums}[j])$​​，其中 $j<i$​。将 $\textit{nums}[i]$​ 和 $\textit{nums}[j]$​ 分别当作等差数列的尾项和倒数第二项，则该等差数列的公差 $d=\textit{nums}[i]-\textit{nums}[j]$​​​。由于公差相同，我们可以将 $\textit{nums}[i]$​ 加到以 $\textit{nums}[j]$​ 为尾项，公差为 d 的弱等差子序列的末尾，这对应着状态转移 $f[i][d] += f[j][d]$​。同时，$(\textit{nums}[i],\textit{nums}[j])$​ 这一对元素也可以当作一个弱等差子序列，故有状态转移

$$
f[i][d] += f[j][d] + 1
$$

```
dp[i][d](弱等差的数目) = dp[j][d](真等差的数目) + 1(假等差的数目)
```

由于题目要统计的等差子序列至少有三个元素，我们回顾上述二重循环，其中「将 $\textit{nums}[i]$ 加到以 $\textit{nums}[j]$ 为尾项，公差为 d 的弱等差子序列的末尾」这一操作，实际上就构成了一个至少有三个元素的等差子序列，因此我们将循环中的 $f[j][d]$​ 累加，即为答案。

代码实现时，由于 $\textit{nums}[i]$ 的范围很大，所以计算出的公差的范围也很大，我们可以将状态转移数组 f 的第二维用哈希表代替。

#### 复杂度分析

- 时间复杂度：$O(n^2)$，其中 n 是数组 $\textit{nums}$ 的长度。
- 空间复杂度：$O(n^2)$。


