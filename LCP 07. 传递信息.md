# 题目

小朋友 A 在和 ta 的小伙伴们玩传信息游戏，游戏规则如下：

1. 有 n 名玩家，所有玩家编号分别为 0 ～ n-1，其中小朋友 A 的编号为 0
2. 每个玩家都有固定的若干个可传信息的其他玩家（也可能没有）。传信息的关系是单向的（比如 A 可以向 B 传信息，但 B 不能向 A 传信息）。
3. 每轮信息必须需要传递给另一个人，且信息可重复经过同一个人

给定总玩家数 `n`，以及按 `[玩家编号,对应可传递玩家编号]` 关系组成的二维数组 `relation`。返回信息从小 A (编号 0 ) 经过 `k` 轮传递到编号为 n-1 的小伙伴处的方案数；若不能到达，返回 0。

**示例 1：**

> 输入：`n = 5, relation = [[0,2],[2,1],[3,4],[2,3],[1,4],[2,0],[0,4]], k = 3`
>
> 输出：`3`
>
> 解释：信息从小 A 编号 0 处开始，经 3 轮传递，到达编号 4。共有 3 种方案，分别是 0->2->0->4， 0->2->1->4， 0->2->3->4。

**示例 2：**

> 输入：`n = 3, relation = [[0,2],[2,1]], k = 2`
>
> 输出：`0`
>
> 解释：信息不能从小 A 处经过 2 轮传递到编号 2

**限制：**

- `2 <= n <= 10`
- `1 <= k <= 5`
- `1 <= relation.length <= 90, 且 relation[i].length == 2`
- `0 <= relation[i][0],relation[i][1] < n 且 relation[i][0] != relation[i][1]`

## 我的解法

```python
class Solution:
    def numWays(self, n: int, relation: List[List[int]], k: int) -> int:
            
        way = 0
        q = deque([(0, 0)])
        while q:
            statu, step = q.popleft()
            if step > k: break
            for relate in relation:
                if relate[0] == statu:
                    q.append((relate[1], step + 1))
            if statu == n - 1 and step == k:
                way += 1
        
        return way
```

思路正确，但是代码效率堪忧！

## 其他解法

### 深度优先搜索

```c++
class Solution {
public:
    int numWays(int n, vector<vector<int>> &relation, int k) {
        vector<vector<int>> edges(n);
        for (auto &edge : relation) {
            int src = edge[0], dst = edge[1];
            edges[src].push_back(dst);
        }

        int ways = 0;
        function<void(int, int)> dfs = [&](int index, int steps) {
            if (steps == k) {
                if (index == n - 1) {
                    ++ways;
                }
                return;
            }
            for (int to : edges[index]) {
                dfs(to, steps + 1);
            }
        };
        dfs(0, 0);
        return ways;
    }
};
```

```java
class Solution {
    int ways, n, k;
    List<List<Integer>> edges;

    public int numWays(int n, int[][] relation, int k) {
        ways = 0;
        this.n = n;
        this.k = k;
        edges = new ArrayList<List<Integer>>();
        for (int i = 0; i < n; i++) {
            edges.add(new ArrayList<Integer>());
        }
        for (int[] edge : relation) {
            int src = edge[0], dst = edge[1];
            edges.get(src).add(dst);
        }
        dfs(0, 0);
        return ways;
    }

    public void dfs(int index, int steps) {
        if (steps == k) {
            if (index == n - 1) {
                ways++;
            }
            return;
        }
        List<Integer> list = edges.get(index);
        for (int nextIndex : list) {
            dfs(nextIndex, steps + 1);
        }
    }
}
```

```python
class Solution:
    def numWays(self, n: int, relation: List[int], k: int) -> int:
        self.ways, self.n, self.k = 0, n, k
        self.edges = collections.defaultdict(list)
        for src, dst in relation:
            self.edges[src].append(dst)

        self.dfs(0,0)
        return self.ways 

    def dfs(self, index, steps):
        if steps == self.k:
            if index == self.n-1:
                self.ways += 1
            return
        for to in self.edges[index]:
            self.dfs(to, steps+1)
```

可以把传信息的关系看成有向图，每个玩家对应一个节点，每个传信息的关系对应一条有向边。如果 x 可以向 y 传信息，则对应从节点 x 到节点 y 的一条有向边。寻找从编号 0 的玩家经过 k 轮传递到编号 n-1 的玩家处的方案数，等价于在有向图中寻找从节点 0 到节点 n−1 的长度为 k 的路径数，同一条路径可以重复经过同一个节点。

可以使用深度优先搜索计算方案数。从节点 0 出发做深度优先搜索，每一步记录当前所在的节点以及经过的轮数，当经过 k 轮时，如果位于节点 n−1，则将方案数加 1。搜索结束之后，即可得到总的方案数。

具体实现方面，可以对传信息的关系进行预处理，使用列表存储有向边的关系，即可在 O(1) 的时间内得到特定节点的相邻节点（即可以沿着有向边一步到达的节点）。

#### 复杂度分析

- 时间复杂度：$O(n^k)$。最多需要遍历 k 层，每层遍历最多有 O(n) 个分支。

- 空间复杂度：O(n+m+k)。其中 m 为 $\textit{relation}$ 数组的长度。空间复杂度主要取决于图的大小和递归调用栈的深度，保存有向图信息所需空间为 O(n+m)，递归调用栈的深度不会超过 k。


### 广度优先搜索

```c++
class Solution {
public:
    int numWays(int n, vector<vector<int>> &relation, int k) {
        vector<vector<int>> edges(n);
        for (auto &edge : relation) {
            int src = edge[0], dst = edge[1];
            edges[src].push_back(dst);
        }

        int steps = 0;
        queue<int> que;
        que.push(0);
        while (!que.empty() && steps < k) {
            steps++;
            int size = que.size();
            for (int i = 0; i < size; i++) {
                int index = que.front();
                que.pop();
                for (auto &nextIndex : edges[index]) {
                    que.push(nextIndex);
                }
            }
        }

        int ways = 0;
        if (steps == k) {
            while (!que.empty()) {
                if (que.front() == n - 1) {
                    ways++;
                }
                que.pop();
            }
        }
        return ways;
    }
};
```

```java
class Solution {
    public int numWays(int n, int[][] relation, int k) {
        List<List<Integer>> edges = new ArrayList<List<Integer>>();
        for (int i = 0; i < n; i++) {
            edges.add(new ArrayList<Integer>());
        }
        for (int[] edge : relation) {
            int src = edge[0], dst = edge[1];
            edges.get(src).add(dst);
        }

        int steps = 0;
        Queue<Integer> queue = new LinkedList<Integer>();
        queue.offer(0);
        while (!queue.isEmpty() && steps < k) {
            steps++;
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int index = queue.poll();
                List<Integer> list = edges.get(index);
                for (int nextIndex : list) {
                    queue.offer(nextIndex);
                }
            }
        }

        int ways = 0;
        if (steps == k) {
            while (!queue.isEmpty()) {
                if (queue.poll() == n - 1) {
                    ways++;
                }
            }
        }
        return ways;
    }
}
```

```python
class Solution:
    def numWays(self, n: int, relation: List[int], k: int) -> int:
        edges = collections.defaultdict(list)
        for edge in relation:
            src = edge[0] 
            dst = edge[1]
            edges[src].append(dst)
        steps = 0
        queue = collections.deque([0])
        while queue and steps < k:
            steps += 1
            size = len(queue)
            for i in range(size):
                index = queue.popleft()
                to = edges[index]
                for nextIndex in to:
                    queue.append(nextIndex)
        ways = 0
        if steps == k:
            while queue:
                if queue.popleft() == n - 1:
                    ways += 1    
        return ways 
```

也可以使用广度优先搜索计算方案数。从节点 0 出发做广度优先搜索，当遍历到 k 层时，如果位于节点 n−1，则将方案数加 1。搜索结束之后，即可得到总的方案数。

#### 复杂度分析

时间复杂度：$O(n^k)$。最多需要遍历 k 层，每层遍历最多有 O(n)个分支。

空间复杂度：$O(n+m+n^k)$。其中 m 为 $\textit{relation}$ 数组的长度。空间复杂度主要取决于图的大小和队列的大小，保存有向图信息所需空间为 O(n+m)，由于每层遍历最多有 O(n)个分支，因此遍历到 k 层时，队列的大小为 $O(n^k)$。

### 动态规划

#### 空间复杂度未优化

```c++
class Solution {
public:
    int numWays(int n, vector<vector<int>>& relation, int k) {
        vector<vector<int>> dp(k + 1, vector<int>(n));
        dp[0][0] = 1;
        for (int i = 0; i < k; i++) {
            for (auto& edge : relation) {
                int src = edge[0], dst = edge[1];
                dp[i + 1][dst] += dp[i][src];
            }
        }
        return dp[k][n - 1];
    }
};
```

```java
class Solution {
    public int numWays(int n, int[][] relation, int k) {
        int[][] dp = new int[k + 1][n];
        dp[0][0] = 1;
        for (int i = 0; i < k; i++) {
            for (int[] edge : relation) {
                int src = edge[0], dst = edge[1];
                dp[i + 1][dst] += dp[i][src];
            }
        }
        return dp[k][n - 1];
    }
}
```

```python
class Solution:
    def numWays(self, n: int, relation: List[List[int]], k: int) -> int:
        dp = [[0] * (n + 1) for _ in range(k + 1)]
        dp[0][0] = 1
        for i in range(k):
            for edge in relation:
                src = edge[0]
                dst = edge[1]
                dp[i + 1][dst] += dp[i][src]
        return dp[k][n - 1]
```

#### 空间复杂度优化

```c++
class Solution {
public:
    int numWays(int n, vector<vector<int>>& relation, int k) {
        vector<int> dp(n);
        dp[0] = 1;
        for (int i = 0; i < k; i++) {
            vector<int> next(n);
            for (auto& edge : relation) {
                int src = edge[0], dst = edge[1];
                next[dst] += dp[src];
            }
            dp = next;
        }
        return dp[n - 1];
    }
};
```

```java
class Solution {
    public int numWays(int n, int[][] relation, int k) {
        int[] dp = new int[n];
        dp[0] = 1;
        for (int i = 0; i < k; i++) {
            int[] next = new int[n];
            for (int[] edge : relation) {
                int src = edge[0], dst = edge[1];
                next[dst] += dp[src];
            }
            dp = next;
        }
        return dp[n - 1];
    }
}
```

```python
class Solution:
    def numWays(self, n: int, relation: List[List[int]], k: int) -> int:
        dp = [0 for _ in range(n + 1)]
        dp[0] = 1
        for i in range(k):
            next = [0 for _ in range(n + 1)]
            for edge in relation:
                src = edge[0]
                dst = edge[1]
                next[dst] += dp[src]
            dp = next
        return dp[n - 1]
```

前两种方法都是通过在图中搜索计算方案数。可以换一个思路，这道题是计数问题，可以使用动态规划的方法解决。

定义动态规划的状态 $\textit{dp}[i][j]$ 为经过 i 轮传递到编号 j 的玩家的方案数，其中 $0 \le i \le k$，$0 \le j < n$。

由于从编号 0 的玩家开始传递，当 i=0 时，一定位于编号 0 的玩家，不会传递到其他玩家，因此动态规划的边界情况如下：

$$
\textit{dp}[0][j]= \begin{cases} 1, & j=0 \\ 0, & j \ne 0 \end{cases}
$$

对于传信息的关系 $[\textit{src},\textit{dst}]$，如果第 i 轮传递到编号 $\textit{src}$ 的玩家，则第i+1 轮可以从编号 $\textit{src}$ 的玩家传递到编号 $\textit{dst}$ 的玩家。因此在计算$ \textit{dp}[i+1][\textit{dst}]$时，需要考虑可以传递到编号$ \textit{dst}$ 的所有玩家。由此可以得到动态规划的状态转移方程，其中 $0 \le i < k$：

$$
\textit{dp}[i+1][\textit{dst}]=\sum_{[\textit{src},\textit{dst}] \in \textit{relation}} \textit{dp}[i][\textit{src}]
$$
最终得到 $\textit{dp}[k][n-1]$ 即为总的方案数。

上述实现的空间复杂度是 O(kn)。由于当 i>0 时，$\textit{dp}[i][]$ 的值只和 $\textit{dp}[i-1][]$的值有关，因此可以将二维数组变成一维数组，将空间复杂度优化到 O(n)。

#### 复杂度分析

时间复杂度：O(km)。其中 m 为 $\textit{relation}$ 数组的长度。

空间复杂度：O(n)。

