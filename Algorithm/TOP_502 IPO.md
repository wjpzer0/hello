# 题目

假设 力扣（LeetCode）即将开始 **IPO** 。为了以更高的价格将股票卖给风险投资公司，力扣 希望在 IPO 之前开展一些项目以增加其资本。 由于资源有限，它只能在 IPO 之前完成最多 `k` 个不同的项目。帮助 力扣 设计完成最多 `k` 个不同项目后得到最大总资本的方式。

给你 `n` 个项目。对于每个项目 `i` ，它都有一个纯利润 `profits[i]` ，和启动该项目需要的最小资本 `capital[i]` 。

最初，你的资本为 `w` 。当你完成一个项目时，你将获得纯利润，且利润将被添加到你的总资本中。

总而言之，从给定项目中选择 **最多** `k` 个不同项目的列表，以 **最大化最终资本** ，并输出最终可获得的最多资本。

答案保证在 32 位有符号整数范围内。

**示例 1：**

```
输入：k = 2, w = 0, profits = [1,2,3], capital = [0,1,1]
输出：4
解释：
由于你的初始资本为 0，你仅可以从 0 号项目开始。
在完成后，你将获得 1 的利润，你的总资本将变为 1。
此时你可以选择开始 1 号或 2 号项目。
由于你最多可以选择两个项目，所以你需要完成 2 号项目以获得最大的资本。
因此，输出最后最大化的资本，为 0 + 1 + 3 = 4。
```

**示例 2：**

```
输入：k = 3, w = 0, profits = [1,2,3], capital = [0,1,2]
输出：6
```

**提示：**

- $1 <= k <= 10^5$
- $0 <= w <= 10^9$
- `n == profits.length`
- `n == capital.length`
- $1 <= n <= 10^5$
- $0 <= profits[i] <= 10^4$
- $0 <= capital[i] <= 10^9$

## 我的解法

未解出，想到了贪心！

## 其他解法

### 利用堆的贪心算法

```c++
typedef pair<int,int> pii;

class Solution {
public:
    int findMaximizedCapital(int k, int w, vector<int>& profits, vector<int>& capital) {
        int n = profits.size();
        int curr = 0;
        priority_queue<int, vector<int>, less<int>> pq;
        vector<pii> arr;

        for (int i = 0; i < n; ++i) {
            arr.push_back({capital[i], profits[i]});
        }
        sort(arr.begin(), arr.end());
        for (int i = 0; i < k; ++i) {
            while (curr < n && arr[curr].first <= w) {
                pq.push(arr[curr].second);
                curr++;
            }
            if (!pq.empty()) {
                w += pq.top();
                pq.pop();
            } else {
                break;
            }
        }

        return w;
    }
};
```

```java
class Solution {
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        int curr = 0;
        int[][] arr = new int[n][2];

        for (int i = 0; i < n; ++i) {
            arr[i][0] = capital[i];
            arr[i][1] = profits[i];
        }
        Arrays.sort(arr, (a, b) -> a[0] - b[0]);

        PriorityQueue<Integer> pq = new PriorityQueue<>((x, y) -> y - x);
        for (int i = 0; i < k; ++i) {
            while (curr < n && arr[curr][0] <= w) {
                pq.add(arr[curr][1]);
                curr++;
            }
            if (!pq.isEmpty()) {
                w += pq.poll();
            } else {
                break;
            }
        }

        return w;
    }
}
```

```python
class Solution:
    def findMaximizedCapital(self, k: int, w: int, profits: List[int], capital: List[int]) -> int:
        if w >= max(capital):
            return w + sum(nlargest(k, profits))

        n = len(profits)
        curr = 0
        arr = [(capital[i], profits[i]) for i in range(n)]
        arr.sort(key = lambda x : x[0])
        
        pq = []
        for _ in range(k):
            while curr < n and arr[curr][0] <= w:
                heappush(pq, -arr[curr][1])
                curr += 1

            if pq:
                w -= heappop(pq)
            else:
                break
        
        return w
```

#### 思路与算法

我们首先思考，如果不限制次数下我们可以获取的最大利润，我们应该如何处理？我们只需将所有的项目按照资本的大小进行排序，依次购入项目 i，同时手中持有的资本增加 $\textit{profits}[i]$，直到手中的持有的资本无法启动当前的项目为止。

- 如果初始资本 $w \ge \max(\textit{capital})$，我们直接返回利润中的 kk 个最大元素的和即可。

- 当前的题目中限定了可以选择的次数最多为 k 次，这就意味着我们应该贪心地保证选择每次投资的项目获取的利润最大，这样就能保持选择 k 次后获取的利润最大。

- 我们首先将项目按照所需资本的从小到大进行排序，每次进行选择时，假设当前手中持有的资本为 w，我们应该从所有投入资本小于等于 w 的项目中选择利润最大的项目 j，然后此时我们更新手中持有的资本为$ w + \textit{profits}[j]$，同时再从所有花费资本小于等于 $w + \textit{profits}[j]$ 的项目中选择，我们按照上述规则不断选择 k 次即可。

- 我们利用大根堆的特性，我们将所有能够投资的项目的利润全部压入到堆中，每次从堆中取出最大值，然后更新手中持有的资本，同时将待选的项目利润进入堆，不断重复上述操作。

- 如果当前的堆为空，则此时我们应当直接返回。


#### 复杂度分析

- 时间复杂度：$O((n + k) \log n)$，其中 n 是数组 $\textit{profits}$ 和 $\textit{capital}$ 的长度，k 表示最多的选择数目。我们需要 $O(n \log n)$ 的时间复杂度来来创建和排序项目，往堆中添加元素的时间不超过 $O(n \log n)$，每次从堆中取出最大值并更新资本的时间为 $O(k \log n)$，因此总的时间复杂度为 $O(n \log n + n \log n + k \log n) = O((n + k) \log n)$。

- 空间复杂度：O(n)，其中 n 是数组 $\textit{profits}$ 和 $\textit{capital}$ 的长度。空间复杂度主要取决于创建用于排序的数组和大根堆。


