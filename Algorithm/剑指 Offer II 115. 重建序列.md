# 题目

给定一个长度为 `n` 的整数数组 `nums` ，其中 `nums` 是范围为 `[1，n]` 的整数的排列。还提供了一个 2D 整数数组 `sequences` ，其中 `sequences[i]` 是 `nums` 的子序列。
检查 `nums` 是否是唯一的最短 **超序列** 。最短 **超序列** 是 **长度最短** 的序列，并且所有序列 `sequences[i]` 都是它的子序列。对于给定的数组 `sequences` ，可能存在多个有效的 **超序列** 。

- 例如，对于 `sequences = [[1,2],[1,3]]` ，有两个最短的 **超序列** ，`[1,2,3]` 和 `[1,3,2]` 。
- 而对于 `sequences = [[1,2],[1,3],[1,2,3]]` ，唯一可能的最短 **超序列** 是 `[1,2,3]` 。`[1,2,3,4]` 是可能的超序列，但不是最短的。

*如果 `nums` 是序列的唯一最短 **超序列** ，则返回 `true` ，否则返回 `false` 。*
**子序列** 是一个可以通过从另一个序列中删除一些元素或不删除任何元素，而不改变其余元素的顺序的序列。

**示例 1：**

```
输入：nums = [1,2,3], sequences = [[1,2],[1,3]]
输出：false
解释：有两种可能的超序列：[1,2,3]和[1,3,2]。
序列 [1,2] 是[1,2,3]和[1,3,2]的子序列。
序列 [1,3] 是[1,2,3]和[1,3,2]的子序列。
因为 nums 不是唯一最短的超序列，所以返回false。
```

**示例 2：**

```
输入：nums = [1,2,3], sequences = [[1,2]]
输出：false
解释：最短可能的超序列为 [1,2]。
序列 [1,2] 是它的子序列：[1,2]。
因为 nums 不是最短的超序列，所以返回false。
```

**示例 3：**

```
输入：nums = [1,2,3], sequences = [[1,2],[1,3],[2,3]]
输出：true
解释：最短可能的超序列为[1,2,3]。
序列 [1,2] 是它的一个子序列：[1,2,3]。
序列 [1,3] 是它的一个子序列：[1,2,3]。
序列 [2,3] 是它的一个子序列：[1,2,3]。
因为 nums 是唯一最短的超序列，所以返回true。
```

**提示：**

- `n == nums.length`
- $1 <= n <= 10^4$
- `nums` 是 `[1, n]` 范围内所有整数的排列
- $1 <= sequences.length <= 10^4$
- $1 <= sequences[i].length <= 10^4$
- $1 <= sum(sequences[i].length) <= 10^5$
- `1 <= sequences[i][j] <= n`
- `sequences` 的所有数组都是 **唯一** 的
- `sequences[i]` 是 `nums` 的一个子序列

##  我的解法

可能想到了图，但是思路仍然不够清晰!

## 其他解法

### 拓扑排序

```c++
class Solution {
public:
    bool sequenceReconstruction(vector<int>& nums, vector<vector<int>>& sequences) {
        int n = nums.size();
        vector<int> indegrees(n + 1);
        vector<unordered_set<int>> graph(n + 1);
        for (auto &sequence : sequences) {
            for (int i = 1; i < sequence.size(); i++) {
                int prev = sequence[i - 1], next = sequence[i];
                if (!graph[prev].count(next)) {
                    graph[prev].emplace(next);
                    indegrees[next]++;
                }
            }
        }
        queue<int> qu;
        for (int i = 1; i <= n; i++) {
            if (indegrees[i] == 0) {
                qu.emplace(i);
            }
        }
        while (!qu.empty()) {
            if (qu.size() > 1) {
                return false;
            }
            int num = qu.front();
            qu.pop();
            for (int next : graph[num]) {
                indegrees[next]--;
                if (indegrees[next] == 0) {
                    qu.emplace(next);
                }
            }
        }
        return true;
    }
};
```

```java
class Solution {
    public boolean sequenceReconstruction(int[] nums, int[][] sequences) {
        int n = nums.length;
        int[] indegrees = new int[n + 1];
        Set<Integer>[] graph = new Set[n + 1];
        for (int i = 1; i <= n; i++) {
            graph[i] = new HashSet<Integer>();
        }
        for (int[] sequence : sequences) {
            int size = sequence.length;
            for (int i = 1; i < size; i++) {
                int prev = sequence[i - 1], next = sequence[i];
                if (graph[prev].add(next)) {
                    indegrees[next]++;
                }
            }
        }
        Queue<Integer> queue = new ArrayDeque<Integer>();
        for (int i = 1; i <= n; i++) {
            if (indegrees[i] == 0) {
                queue.offer(i);
            }
        }
        while (!queue.isEmpty()) {
            if (queue.size() > 1) {
                return false;
            }
            int num = queue.poll();
            Set<Integer> set = graph[num];
            for (int next : set) {
                indegrees[next]--;
                if (indegrees[next] == 0) {
                    queue.offer(next);
                }
            }
        }
        return true;
    }
}
```

```python
class Solution:
    def sequenceReconstruction(self, nums: List[int], sequences: List[List[int]]) -> bool:
        n = len(nums)
        g = [[] for _ in range(n)]
        inDeg = [0] * n
        for sequence in sequences:
            for x, y in pairwise(sequence):
                g[x - 1].append(y - 1)
                inDeg[y - 1] += 1

        q = deque([i for i, d in enumerate(inDeg) if d == 0])
        while q:
            if len(q) > 1:
                return False
            x = q.popleft()
            for y in g[x]:
                inDeg[y] -= 1
                if inDeg[y] == 0:
                    q.append(y)
        return True
```

#### 思路和算法

由于 $\textit{sequences}$中的每个序列都是 $\textit{nums}$的子序列，因此每个序列中的数字顺序都和 $\textit{nums}$中的数字顺序一致。为了判断 $\textit{nums}$是不是序列的唯一最短超序列，只需要判断根据 $\textit{sequences}$ 中的每个序列构造超序列的结果是否唯一。

可以将 $\textit{sequences}$中的所有序列看成有向图，数字 1 到 n 分别表示图中的 n 个结点，每个序列中的相邻数字表示的结点之间存在一条有向边。根据给定的序列构造超序列等价于有向图的拓扑排序。

首先根据有向边计算每个结点的入度，然后将所有入度为 0 的结点添加到队列中，进行拓扑排序。每一轮拓扑排序时，队列中的元素个数表示可以作为超序列下一个数字的元素个数，根据队列中的元素个数，执行如下操作。

如果队列中的元素个数大于 1，则超序列的下一个数字不唯一，因此 $\textit{nums}$不是唯一的最短超序列，返回 $\text{false}$。

如果队列中的元素个数等于 1，则超序列的下一个数字是队列中唯一的数字。将该数字从队列中取出，将该数字指向的每个数字的入度减 1，并将入度变成 0 的数字添加到队列中。

重复上述过程，直到出现队列中的元素个数不等于 1 的情况。

如果队列中的元素个数大于 1，则 $\textit{nums}$不是唯一的最短超序列，返回 $\text{false}$。

如果队列为空，则完整的拓扑排序结束，$\textit{nums}$是唯一的最短超序列，返回 $\text{true}$。

#### 证明

如果拓扑排序的过程中，有一轮的队列中的元素个数大于 1，则由于超序列的下一个数字有多种可能，因此 $\textit{nums}$ 不是唯一的最短超序列，这一点颇为直观。需要证明的是：当队列为空时，完整的拓扑排序结束，$\textit{nums}$是唯一的最短超序列。

证明一：只有当 $\textit{nums}$中的所有数字都在至少一个序列中出现时，才可能执行完整的拓扑排序。

由于 $\textit{sequences}$中的每个序列都是 $\textit{nums}$的子序列，因此序列中不存在环，对于所有在至少一个序列中出现的数字，这些数字中一定存在入度为 0 的数字。

如果一个数字没有在任何序列中出现，则该数字的入度为 0，即初始时就有多个数字的入度为 0，超序列的第一个数字就不唯一，此时会提前返回 $\text{false}$。因此如果执行完整的拓扑排序，则 $\textit{nums}$中的所有数字都在至少一个序列中出现。

证明二：当执行完整的拓扑排序时，得到的超序列的长度为 n。

由于序列中不存在环，因此当完整的拓扑排序结束时，所有在至少一个序列中出现过的数字都在超序列中。由于执行完整的拓扑排序意味着 $\textit{nums}$中的所有数字都在至少一个序列中出现，因此 $\textit{nums}$中的所有数字都在超序列中，即超序列的长度为 n。

综上所述，当完整的拓扑排序结束时，$\textit{nums}$是唯一的最短超序列。

#### 复杂度分析

- 时间复杂度：O(n + m)，其中 n 是数组 $\textit{nums}$的长度，m 是 $\textit{sequences}$中的所有序列长度之和。建图和拓扑排序都需要 O(n + m) 的时间。

- 空间复杂度：O(n + m)，其中n 是数组 $\textit{nums}$的长度，m 是 $\textit{sequences}$中的所有序列长度之和。需要 O(n + m) 的空间存储图信息，需要 O(n) 的空间存储每个结点的入度，拓扑排序过程中队列空间是 O(n)。


