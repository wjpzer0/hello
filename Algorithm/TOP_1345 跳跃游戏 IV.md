# 题目

给你一个整数数组 `arr` ，你一开始在数组的第一个元素处（下标为 0）。

每一步，你可以从下标 `i` 跳到下标：

- `i + 1` 满足：`i + 1 < arr.length`
- `i - 1` 满足：`i - 1 >= 0`
- `j` 满足：`arr[i] == arr[j]` 且 `i != j`

请你返回到达数组最后一个元素的下标处所需的 **最少操作次数** 。

注意：任何时候你都不能跳到数组外面。

 

**示例 1：**

```
输入：arr = [100,-23,-23,404,100,23,23,23,3,404]
输出：3
解释：那你需要跳跃 3 次，下标依次为 0 --> 4 --> 3 --> 9 。下标 9 为数组的最后一个元素的下标。
```

**示例 2：**

```
输入：arr = [7]
输出：0
解释：一开始就在最后一个元素处，所以你不需要跳跃。
```

**示例 3：**

```
输入：arr = [7,6,9,6,9,6,9,7]
输出：1
解释：你可以直接从下标 0 处跳到下标 7 处，也就是数组的最后一个元素处。
```

**示例 4：**

```
输入：arr = [6,1,9]
输出：2
```

**示例 5：**

```
输入：arr = [11,22,7,7,7,7,7,7,7,22,13]
输出：3
```

**提示：**

- $1 <= arr.length <= 5 * 10^4$
- $-10^8 <= arr[i] <= 10^8$

## 我的解法

未解出！想到了动态规划，但没有写出来！该题的总结是图的最短路径！

## 其他解法

### 广度优先搜索

```c++
class Solution {
public:
    int minJumps(vector<int>& arr) {
        unordered_map<int, vector<int>> idxSameValue;
        for (int i = 0; i < arr.size(); i++) {
            idxSameValue[arr[i]].push_back(i);
        }
        unordered_set<int> visitedIndex;
        queue<pair<int, int>> q;
        q.emplace(0, 0);
        visitedIndex.emplace(0);
        while (!q.empty()) {
            auto [idx, step] = q.front();
            q.pop();
            if (idx == arr.size() - 1) {
                return step;
            }
            int v = arr[idx];
            step++;
            if (idxSameValue.count(v)) {
                for (auto & i : idxSameValue[v]) {
                    if (!visitedIndex.count(i)) {
                        visitedIndex.emplace(i);
                        q.emplace(i, step);
                    }
                }
                idxSameValue.erase(v);
            }
            if (idx + 1 < arr.size() && !visitedIndex.count(idx + 1)) {
                visitedIndex.emplace(idx + 1);
                q.emplace(idx + 1, step);
            }
            if (idx - 1 >= 0 && !visitedIndex.count(idx - 1)) {
                visitedIndex.emplace(idx - 1);
                q.emplace(idx - 1, step);
            }
        }
        return -1;
    }
};
```

```java
class Solution {
    public int minJumps(int[] arr) {
        Map<Integer, List<Integer>> idxSameValue = new HashMap<Integer, List<Integer>>();
        for (int i = 0; i < arr.length; i++) {
            idxSameValue.putIfAbsent(arr[i], new ArrayList<Integer>());
            idxSameValue.get(arr[i]).add(i);
        }
        Set<Integer> visitedIndex = new HashSet<Integer>();
        Queue<int[]> queue = new ArrayDeque<int[]>();
        queue.offer(new int[]{0, 0});
        visitedIndex.add(0);
        while (!queue.isEmpty()) {
            int[] idxStep = queue.poll();
            int idx = idxStep[0], step = idxStep[1];
            if (idx == arr.length - 1) {
                return step;
            }
            int v = arr[idx];
            step++;
            if (idxSameValue.containsKey(v)) {
                for (int i : idxSameValue.get(v)) {
                    if (visitedIndex.add(i)) {
                        queue.offer(new int[]{i, step});
                    }
                }
                idxSameValue.remove(v);
            }
            if (idx + 1 < arr.length && visitedIndex.add(idx + 1)) {
                queue.offer(new int[]{idx + 1, step});
            }
            if (idx - 1 >= 0 && visitedIndex.add(idx - 1)) {
                queue.offer(new int[]{idx - 1, step});
            }
        }
        return -1;
    }
}
```

```python
class Solution:
    def minJumps(self, arr: List[int]) -> int:
        idxSameValue = defaultdict(list)
        for i, a in enumerate(arr):
            idxSameValue[a].append(i)
        visitedIndex = set()
        q = deque()
        q.append([0, 0])
        visitedIndex.add(0)
        while q:
            idx, step = q.popleft()
            if idx == len(arr) - 1:
                return step
            v = arr[idx]
            step += 1
            for i in idxSameValue[v]:
                if i not in visitedIndex:
                    visitedIndex.add(i)
                    q.append([i, step])
            del idxSameValue[v]
            if idx + 1 < len(arr) and (idx + 1) not in visitedIndex:
                visitedIndex.add(idx + 1)
                q.append([idx+1, step])
            if idx - 1 >= 0 and (idx - 1) not in visitedIndex:
                visitedIndex.add(idx - 1)
                q.append([idx-1, step])
```

#### 思路

记数组$ \textit{arr}$ 的长度为 n。题目描述的数组可以抽象为一个无向图，数组元素为图的顶点，相邻下标的元素之间有一条无向边相连，所有值相同元素之间也有无向边相连。每条边的权重都为 1，即此图为无权图。求从第一个元素到最后一个元素的最少操作数，即求从第一个元素到最后一个元素的最短路径长度。求无权图两点间的最短路可以用广度优先搜索来解，时间复杂度为 O(V+E)，其中 V 为图的顶点数，E 为图的边数。

在此题中，V = n，而 E 可达 $O(n^2)$ 数量级，按照常规方法使用广度优先搜索会超时。造成超时的主要原因是所有值相同的元素构成了一个稠密子图，普通的广度优先搜索方法会对这个稠密子图中的所有边都访问一次。但对于无权图的最短路问题，这样的访问是不必要的。在第一次访问到这个子图中的某个节点时，即会将这个子图的所有其他未在队列中的节点都放入队列。在第二次访问到这个子图中的节点时，就不需要去考虑这个子图中的其他节点了，因为所有其他节点都已经在队列中或者已经被访问过了。因此，在用广度优先搜索解决此题时，先需要找出所有的值相同的子图，用一个哈希表 $\textit{idxSameValue}$ 保存。在第一次把这个子图的所有节点放入队列后，把该子图清空，就不会重复访问该子图的其他边了。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为数组 $\textit{arr}$ 的长度。每个元素最多只进入队列一次，最多被判断是否需要进入队列三次。

- 空间复杂度：O(n)，其中 n 为数组 $\textit{arr}$ 的长度。队列，哈希表和哈希集合均最多存储 n 个元素。


