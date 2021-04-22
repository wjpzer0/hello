# 题目

给你一个 m x n 的矩阵 matrix 和一个整数 k ，找出并返回矩阵内部矩形区域的不超过 k 的最大数值和。

题目数据保证总会存在一个数值和不超过 k 的矩形区域。

 ![img](https://assets.leetcode.com/uploads/2021/03/18/sum-grid.jpg)

示例 1：

```
输入：matrix = [[1,0,1],[0,-2,3]], k = 2
输出：2
解释：蓝色边框圈出来的矩形区域 [[0, 1], [-2, 3]] 的数值和是 2，且 2 是不超过 k 的最大数字（k = 2）。
```


示例 2：

```
输入：matrix = [[2,2,-1]], k = 3
输出：3
```


提示：

- m == matrix.length
- n == matrix[i].length
- 1 <= m, n <= 100
- -100 <= matrix[i]\[j] <= 100
- $-10^5 <= k <= 10^5$

## 我的解法

未解出，想到了累加，但是没想到筛选！

## 其他解法

### 有序集合

```c++
class Solution {
public:
    int maxSumSubmatrix(vector<vector<int>> &matrix, int k) {
        int ans = INT_MIN;
        int m = matrix.size(), n = matrix[0].size();
        for (int i = 0; i < m; ++i) { // 枚举上边界
            vector<int> sum(n);
            for (int j = i; j < m; ++j) { // 枚举下边界
                for (int c = 0; c < n; ++c) {
                    sum[c] += matrix[j][c]; // 更新每列的元素和
                }
                set<int> sumSet{0};
                int s = 0;
                for (int v : sum) {
                    s += v;
                    auto lb = sumSet.lower_bound(s - k);
                    if (lb != sumSet.end()) {
                        ans = max(ans, s - *lb);
                    }
                    sumSet.insert(s);
                }
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int ans = Integer.MIN_VALUE;
        int m = matrix.length, n = matrix[0].length;
        for (int i = 0; i < m; ++i) { // 枚举上边界
            int[] sum = new int[n];
            for (int j = i; j < m; ++j) { // 枚举下边界
                for (int c = 0; c < n; ++c) {
                    sum[c] += matrix[j][c]; // 更新每列的元素和
                }
                TreeSet<Integer> sumSet = new TreeSet<Integer>();
                sumSet.add(0);
                int s = 0;
                for (int v : sum) {
                    s += v;
                    Integer ceil = sumSet.ceiling(s - k);
                    if (ceil != null) {
                        ans = Math.max(ans, s - ceil);
                    }
                    sumSet.add(s);
                }
            }
        }
        return ans;
    }
}
```

```python
from sortedcontainers import SortedList

class Solution:
    def maxSumSubmatrix(self, matrix: List[List[int]], k: int) -> int:
        ans = float("-inf")
        m, n = len(matrix), len(matrix[0])

        for i in range(m):   # 枚举上边界
            total = [0] * n
            for j in range(i, m):   # 枚举下边界
                for c in range(n):
                    total[c] += matrix[j][c]   # 更新每列的元素和
                
                totalSet = SortedList([0])
                s = 0
                for v in total:
                    s += v
                    lb = totalSet.bisect_left(s - k)
                    if lb != len(totalSet):
                        ans = max(ans, s - totalSet[lb])
                    totalSet.add(s)

        return ans
```

#### 思路

我们枚举矩形的上下边界，并计算出该边界内每列的元素和，则原问题转换成了如下一维问题：

给定一个整数数组和一个整数 kk，计算该数组的最大区间和，要求区间和不超过 kk。

定义长度为 n 的数组 a 的前缀和

$$
S_i = \begin{cases} 0&i=0\\ a_0+a_1+\cdots+a_{i-1}&1\le i\le n \end{cases}
$$

则区间 [l,r) 的区间和 $a_l+a_{l+1}+\cdots+a_{r-1}$可以表示为$ S_r-S_l$。

枚举rr，上述问题的约束 $S_r-S_l\le k$可以转换为 $S_l\ge S_r-k$。要使 $S_r-S_l$尽可能大，则需要寻找最小的满足 $S_l\ge S_r-k$ 的 $S_l$。我们可以在枚举 r 的同时维护一个存储 $S_i\ (i<r)$的有序集合，则可以在 O(logn) 的时间内二分找到符合要求的 $S_l$。

#### 复杂度分析

- 时间复杂度：$O(m^2n\log n)$)。其中 m 和 n 分别是矩阵 matrix 的行数和列数。

- 空间复杂度：O(n)。


#### 进阶问题

对于行数远大于列数的情况，枚举矩形的左右边界更优，对应的时间复杂度为 $O(n^2m\log m)$

总之，根据 m 和 n 的大小来细化枚举策略，我们可以做到 $O(\min(m,n)^2\max(m,n)\log\max(m,n))$的时间复杂度。

