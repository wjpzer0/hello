# 题目

给定一个初始元素全部为 **0**，大小为 m*n 的矩阵 **M** 以及在 **M** 上的一系列更新操作。

操作用二维数组表示，其中的每个操作用一个含有两个**正整数 a** 和 **b** 的数组表示，含义是将所有符合 **0 <= i < a** 以及 **0 <= j < b** 的元素 **M[i][j]** 的值都**增加 1**。

在执行给定的一系列操作后，你需要返回矩阵中含有最大整数的元素个数。

**示例 1:**

```
输入: 
m = 3, n = 3
operations = [[2,2],[3,3]]
输出: 4
解释: 
初始状态, M = 
[[0, 0, 0],
 [0, 0, 0],
 [0, 0, 0]]

执行完操作 [2,2] 后, M = 
[[1, 1, 0],
 [1, 1, 0],
 [0, 0, 0]]

执行完操作 [3,3] 后, M = 
[[2, 2, 1],
 [2, 2, 1],
 [1, 1, 1]]

M 中最大的整数是 2, 而且 M 中有4个值为2的元素。因此返回 4。
```

**注意:**

1. m 和 n 的范围是 [1,40000]。
2. a 的范围是 [1,m]，b 的范围是 [1,n]。
3. 操作数目不超过 10000。

## 我的解法

解法思路较为清晰！代码可以优化！

```python
class Solution:
    def maxCount(self, m: int, n: int, ops: List[List[int]]) -> int:
        start = [m, n]
        for i in ops:
            row = i[0]
            column = i[1]
            if row < start[0]:
                start[0] = row
            if column < start[1]:
                start[1] = column
        
        return start[0] * start[1]
```

## 其他解法

### 维护所有操作的交集

```c++
class Solution {
public:
    int maxCount(int m, int n, vector<vector<int>>& ops) {
        int mina = m, minb = n;
        for (const auto& op: ops) {
            mina = min(mina, op[0]);
            minb = min(minb, op[1]);
        }
        return mina * minb;
    }
};
```

```java
class Solution {
    public int maxCount(int m, int n, int[][] ops) {
        int mina = m, minb = n;
        for (int[] op : ops) {
            mina = Math.min(mina, op[0]);
            minb = Math.min(minb, op[1]);
        }
        return mina * minb;
    }
}
```

```python
class Solution:
    def maxCount(self, m: int, n: int, ops: List[List[int]]) -> int:
        mina, minb = m, n
        for a, b in ops:
            mina = min(mina, a)
            minb = min(minb, b)
        return mina * minb
```

#### 思路与算法

对于每一次操作，给定$ (a, b)$，我们会将矩阵中所有满足 $0 \leq i < a$ 以及 $0 \leq j < b$ 的位置 $(i, j)$ 全部加上 1。由于 $a, b$ 均为正整数，那么 $(0, 0)$是满足上述条件，并且最终位置 $(0, 0)$ 的值就等于操作的次数。

因此，我们的任务即为找出矩阵中所有满足要求的次数恰好等于操作次数的位置。假设操作次数为 k，那么 $(i, j)$ 需要满足：

$$
\begin{cases} 0 \leq i < a_0, 0 \leq i < a_1, \cdots, 0 \leq i < a_{k-1} \\ 0 \leq j < b_0, 0 \leq j < b_1, \cdots, 0 \leq j < b_{k-1} \\ \end{cases}
$$
等价于：

$$
\begin{cases}\tag{1} 0 \leq i < \min\limits_k a \\ 0 \leq j < \min\limits_k b \end{cases}
$$
这样一来，我们只需要求出 aa 和 bb 中的最小值，分别记为 $\min\limits_k a $以及 $\min\limits_k b$ ，那么满足 (1) 式的 $(i, j)$ 一共有 $\min\limits_k a \times \min\limits_k b $ 对。

#### 复杂度分析

- 时间复杂度：O(k)，其中 k 是数组 $\textit{ops}$** 的长度。
- 空间复杂度：O(1)。

