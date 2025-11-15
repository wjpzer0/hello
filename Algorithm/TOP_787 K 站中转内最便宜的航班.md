# 题目

有 `n` 个城市通过一些航班连接。给你一个数组 `flights` ，其中 `flights[i] = [fromi, toi, pricei]` ，表示该航班都从城市 `fromi` 开始，以价格 `toi` 抵达 `pricei`。

现在给定所有的城市和航班，以及出发城市 `src` 和目的地 `dst`，你的任务是找到出一条最多经过 `k` 站中转的路线，使得从 `src` 到 `dst` 的 **价格最便宜** ，并返回该价格。 如果不存在这样的路线，则输出 `-1`。

**示例 1：**

```
输入: 
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 1
输出: 200
解释: 
城市航班图如下
从城市 0 到城市 2 在 1 站中转以内的最便宜价格是 200，如图中红色所示。
```

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)

**示例 2：**

```
输入: 
n = 3, edges = [[0,1,100],[1,2,100],[0,2,500]]
src = 0, dst = 2, k = 0
输出: 500
解释: 
城市航班图如下
从城市 0 到城市 2 在 0 站中转以内的最便宜价格是 500，如图中蓝色所示。
```

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)

**提示：**

- `1 <= n <= 100`
- `0 <= flights.length <= (n * (n - 1) / 2)`
- `flights[i].length == 3`
- `0 <= fromi, toi < n`
- `fromi != toi`
- $1 <= pricei <= 10^4$
- 航班没有重复，且不存在自环
- `0 <= src, dst, k < n`
- `src != dst`

## 我的解法

未解出，想到了Dijkstra的解法！但是没写出来！

查看题解后写出！C++写不出来！

```python
import collections
import heapq

class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        if len(flights) == 0:
            return -1

        neighbours = collections.defaultdict(list)
        for i, j, p in flights:
            neighbours[i].append([j, p])
        
        # 检查起点和终点间是否有通路，没有则返回 -1
        visited = set()
        q = [src]
        while q:
            position = q.pop()
            visited.add(position)
            for nei, _ in neighbours[position]:
                if nei not in visited:
                    q.append(nei)
        
        if dst not in visited:
            return -1

        # 用优先队列选择符合条件的最低价格
        pq = [[0, -1, src]]
        while pq:
            price, passed, position = heapq.heappop(pq)
            if position == dst:
                return price
            for nei_position, nei_price in neighbours[position]:
                if passed + 1 <= k:
                    heapq.heappush(pq, [price+nei_price, passed+1, nei_position])

        return -1 
```

## 其他解法

### 动态规划

#### 使用二维数组进行状态转移

```c++
class Solution {
private:
    static constexpr int INF = 10000 * 101 + 1;

public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<int>> f(k + 2, vector<int>(n, INF));
        f[0][src] = 0;
        for (int t = 1; t <= k + 1; ++t) {
            for (auto&& flight: flights) {
                int j = flight[0], i = flight[1], cost = flight[2];
                f[t][i] = min(f[t][i], f[t - 1][j] + cost);
            }
        }
        int ans = INF;
        for (int t = 1; t <= k + 1; ++t) {
            ans = min(ans, f[t][dst]);
        }
        return (ans == INF ? -1 : ans);
    }
};
```

```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        final int INF = 10000 * 101 + 1;
        int[][] f = new int[k + 2][n];
        for (int i = 0; i < k + 2; ++i) {
            Arrays.fill(f[i], INF);
        }
        f[0][src] = 0;
        for (int t = 1; t <= k + 1; ++t) {
            for (int[] flight : flights) {
                int j = flight[0], i = flight[1], cost = flight[2];
                f[t][i] = Math.min(f[t][i], f[t - 1][j] + cost);
            }
        }
        int ans = INF;
        for (int t = 1; t <= k + 1; ++t) {
            ans = Math.min(ans, f[t][dst]);
        }
        return ans == INF ? -1 : ans;
    }
}
```

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        f = [[float("inf")] * n for _ in range(k + 2)]
        f[0][src] = 0
        for t in range(1, k + 2):
            for j, i, cost in flights:
                f[t][i] = min(f[t][i], f[t - 1][j] + cost)
        
        ans = min(f[t][dst] for t in range(1, k + 2))
        return -1 if ans == float("inf") else ans
```

#### 一维数组进行状态转移

```c++
class Solution {
private:
    static constexpr int INF = 10000 * 101 + 1;

public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<int> f(n, INF);
        f[src] = 0;
        int ans = INF;
        for (int t = 1; t <= k + 1; ++t) {
            vector<int> g(n, INF);
            for (auto&& flight: flights) {
                int j = flight[0], i = flight[1], cost = flight[2];
                g[i] = min(g[i], f[j] + cost);
            }
            f = move(g);
            ans = min(ans, f[dst]);
        }
        return (ans == INF ? -1 : ans);
    }
};
```

```java
class Solution {
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        final int INF = 10000 * 101 + 1;
        int[] f = new int[n];
        Arrays.fill(f, INF);
        f[src] = 0;
        int ans = INF;
        for (int t = 1; t <= k + 1; ++t) {
            int[] g = new int[n];
            Arrays.fill(g, INF);
            for (int[] flight : flights) {
                int j = flight[0], i = flight[1], cost = flight[2];
                g[i] = Math.min(g[i], f[j] + cost);
            }
            f = g;
            ans = Math.min(ans, f[dst]);
        }
        return ans == INF ? -1 : ans;
    }
}
```

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        f = [float("inf")] * n
        f[src] = 0
        ans = float("inf")
        for t in range(1, k + 2):
            g = [float("inf")] * n
            for j, i, cost in flights:
                g[i] = min(g[i], f[j] + cost)
            f = g
            ans = min(ans, f[dst])
        
        return -1 if ans == float("inf") else ans
```

#### 思路与算法

我们用 $f[t][i]$ 表示通过恰好 t 次航班，从出发城市 $\textit{src}$ 到达城市 i 需要的最小花费。在进行状态转移时，我们可以枚举最后一次航班的起点 j，即：

$$
f[t][i] = \min_{(j, i) \in \textit{flights}} \big\{ f[t-1][j] + \textit{cost}(j, i) \big\}
$$
其中$ (j, i) \in \textit{flights}$ 表示在给定的航班数组 $\textit{flights}$ 中存在从城市 j 出发到达城市 i 的航班，$\textit{cost}(j, i)$ 表示该航班的花费。该状态转移方程的意义在于，枚举最后一次航班的起点 j，那么前 t−1 次航班的最小花费为 $f[t-1][j]$加上最后一次航班的花费$ \textit{cost}(j, i)$ 中的最小值，即为 $f[t][i]$。

由于我们最多只能中转 k 次，也就是最多搭乘 k+1 次航班，最终的答案即为

$$
f[1][\textit{dst}], f[2][\textit{dst}], \cdots, f[k+1][\textit{dst}]
$$
中的最小值。

#### 细节

当 t=0 时，状态 $f[t][i]$ 表示不搭乘航班到达城市 i 的最小花费，因此有：

$$
f[t][i] = \begin{cases} 0, & i = \textit{src} \\ \infty, & i \neq \textit{src} \end{cases}
$$
也就是说，如果 i 是出发城市 $\textit{src}$，那么花费为 0；否则 $f[0][i]$ 不是一个合法的状态，由于在状态转移方程中我们需要求出的是最小值，因此可以将不合法的状态置为极大值 $\infty$。根据题目中给出的数据范围，航班的花费不超过 $10^4$，最多搭乘航班的次数 k+1 不超过 101，那么在实际的代码编写中，我们只要使得极大值大于 $10^4 \times 101$，就可以将表示不合法状态的极大值与合法状态的花费进行区分。

在状态转移中，我们需要使用二重循环枚举 t 和 i，随后枚举所有满足 $(j, i) \in \textit{flights}$ 的 j，这样做的劣势在于没有很好地利用数组 $\textit{flights}$，为了保证时间复杂度较优，需要将 $\textit{flights}$ 中的所有航班存储在一个新的邻接表中。一种可行的解决方法是，我们只需要使用一重循环枚举 t，随后枚举 $\textit{flights}$ 中的每一个航班 $(j, i, \textit{cost})$，并用 $f[t-1][j] + \textit{cost}$ 更新 f[t][i]$，这样就免去了邻接表的使用。

注意到 $f[t][i]$ 只会从 $f[t-1][..]$ 转移而来，因此我们也可以使用两个长度为 n 的一维数组进行状态转移，减少空间的使用。

