# 题目

假如有一排房子，共 `n` 个，每个房子可以被粉刷成红色、蓝色或者绿色这三种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。

当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 `n x 3` 的正整数矩阵 `costs` 来表示的。

例如，`costs[0][0]` 表示第 0 号房子粉刷成红色的成本花费；`costs[1][2]` 表示第 1 号房子粉刷成绿色的花费，以此类推。

请计算出粉刷完所有房子最少的花费成本。

**示例 1：**

```
输入: costs = [[17,2,17],[16,16,5],[14,3,19]]
输出: 10
解释: 将 0 号房子粉刷成蓝色，1 号房子粉刷成绿色，2 号房子粉刷成蓝色。
     最少花费: 2 + 5 + 3 = 10。
```

**示例 2：**

```
输入: costs = [[7,6,2]]
输出: 2
```

**提示:**

- `costs.length == n`
- `costs[i].length == 3`
- `1 <= n <= 100`
- `1 <= costs[i][j] <= 20`

## 我的解法

一开始想到dfs，但是超时，后面想到动态规划！

```python
class Solution:
    def minCost(self, costs: List[List[int]]) -> int:
        n = len(costs)
        pre = [0, 0, 0]
        for home in range(n):
            newCost = [0, 0, 0]
            for i in range(3):
                minAns = []
                for j, cost in enumerate(pre):
                    if j == i:
                        continue
                    minAns.append(cost + costs[home][i])
                newCost[i] = min(minAns)
            pre = newCost
        return min(pre)
```

## 其他解法

### 动态规划

```c++
class Solution {
public:
    int minCost(vector<vector<int>>& costs) {
        int n = costs.size();
        vector<int> dp(3);
        for (int j = 0; j < 3; j++) {
            dp[j] = costs[0][j];
        }
        for (int i = 1; i < n; i++) {
            vector<int> dpNew(3);
            for (int j = 0; j < 3; j++) {
                dpNew[j] = min(dp[(j + 1) % 3], dp[(j + 2) % 3]) + costs[i][j];
            }
            dp = dpNew;
        }
        return *min_element(dp.begin(), dp.end());
    }   
};
```

```java
class Solution {
    public int minCost(int[][] costs) {
        int n = costs.length;
        int[] dp = new int[3];
        for (int j = 0; j < 3; j++) {
            dp[j] = costs[0][j];
        }
        for (int i = 1; i < n; i++) {
            int[] dpNew = new int[3];
            for (int j = 0; j < 3; j++) {
                dpNew[j] = Math.min(dp[(j + 1) % 3], dp[(j + 2) % 3]) + costs[i][j];
            }
            dp = dpNew;
        }
        return Arrays.stream(dp).min().getAsInt();
    }
}
```

```python
class Solution:
    def minCost(self, costs: List[List[int]]) -> int:
        dp = costs[0]
        for i in range(1, len(costs)):
            dp = [min(dp[j - 1], dp[j - 2]) + c for j, c in enumerate(costs[i])]
        return min(dp)
```

每个房子可以被粉刷成三种颜色中的一种，需要计算在满足相邻房子的颜色不同的情况下粉刷所有房子的最小花费成本。由于当已知粉刷前 i 个房子的最小花费成本时，根据粉刷第 i + 1 号房子的花费成本可以计算粉刷前 i + 1 个房子的最小花费成本，因此可以使用动态规划计算最小花费成本。

由于每个房子可以被粉刷成三种颜色中的一种，因此需要分别考虑粉刷成三种颜色时的最小花费成本。

用 $\textit{dp}[i][j]$ 表示粉刷第 0 号房子到第 i 号房子且第 i 号房子被粉刷成第 j 种颜色时的最小花费成本。由于一共有 n 个房子和 3 种颜色，因此 $0 \le i < n$，$0 \le j < 3$。

当只有第 0 号房子被粉刷时，对于每一种颜色，总花费成本即为将第 0 号房子粉刷成该颜色的花费成本，因此边界条件是：对于任意 $0 \le j < 3$，$\textit{dp}[0][j] = \textit{costs}[0][j]$。

对于 $1 \le i < n$，第 i 号房子和第 i - 1 号房子的颜色必须不同，因此当第 i 号房子被粉刷成某一种颜色时，第 i - 1 号房子只能被粉刷成另外两种颜色之一。当第 i 号房子分别被粉刷成三种颜色时，粉刷第 0 号房子到第 i 号房子的最小花费成本计算如下：

$$
\begin{aligned} \textit{dp}[i][0] &= \min(\textit{dp}[i - 1][1], \textit{dp}[i - 1][2]) + \textit{costs}[i][0] \\ \textit{dp}[i][1] &= \min(\textit{dp}[i - 1][0], \textit{dp}[i - 1][2]) + \textit{costs}[i][1] \\ \textit{dp}[i][2] &= \min(\textit{dp}[i - 1][0], \textit{dp}[i - 1][1]) + \textit{costs}[i][2] \end{aligned}
$$
三种颜色的情况可以合并为一个状态转移方程，对于 $1 \le i < n$ 和 $0 \le j < 3$，状态转移方程如下：

$$
\textit{dp}[i][j] = \min(\textit{dp}[i - 1][(j + 1) \bmod 3], \textit{dp}[i - 1][(j + 2) \bmod 3]) + \textit{costs}[i][j]
$$
计算结束时，$\textit{dp}[n - 1]$ 中的最小值即为粉刷所有房子的最小花费成本。

当 $i \ge 1$ 时，由于$ \textit{dp}[i]$ 的计算只和 $\textit{dp}[i - 1]$ 有关，因此可以使用滚动数组优化空间，将空间复杂度降低到 O(1)。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是房子个数。需要遍历全部房子一次，由于颜色数量固定是三种，因此对于每个房子计算粉刷房子的最小花费成本的时间是 O(1)，总时间复杂度是 O(n)。

- 空间复杂度：O(1)。使用空间优化的方法，只需要维护一个长度为 3 的数组，空间复杂度是 O(1)。


