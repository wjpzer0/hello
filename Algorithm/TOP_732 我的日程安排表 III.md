# 题目

当 `k` 个日程安排有一些时间上的交叉时（例如 `k` 个日程安排都在同一时间内），就会产生 `k` 次预订。

给你一些日程安排 `[start, end)` ，请你在每个日程安排添加后，返回一个整数 `k` ，表示所有先前日程安排会产生的最大 `k` 次预订。

实现一个 `MyCalendarThree` 类来存放你的日程安排，你可以一直添加新的日程安排。

- `MyCalendarThree()` 初始化对象。
- `int book(int start, int end)` 返回一个整数 `k` ，表示日历中存在的 `k` 次预订的最大值。

**示例：**

```
输入：
["MyCalendarThree", "book", "book", "book", "book", "book", "book"]
[[], [10, 20], [50, 60], [10, 40], [5, 15], [5, 10], [25, 55]]
输出：
[null, 1, 1, 2, 3, 3, 3]

解释：
MyCalendarThree myCalendarThree = new MyCalendarThree();
myCalendarThree.book(10, 20); // 返回 1 ，第一个日程安排可以预订并且不存在相交，所以最大 k 次预订是 1 次预订。
myCalendarThree.book(50, 60); // 返回 1 ，第二个日程安排可以预订并且不存在相交，所以最大 k 次预订是 1 次预订。
myCalendarThree.book(10, 40); // 返回 2 ，第三个日程安排 [10, 40) 与第一个日程安排相交，所以最大 k 次预订是 2 次预订。
myCalendarThree.book(5, 15); // 返回 3 ，剩下的日程安排的最大 k 次预订是 3 次预订。
myCalendarThree.book(5, 10); // 返回 3
myCalendarThree.book(25, 55); // 返回 3
```

**提示：**

- $0 <= start < end <= 10^9$
- 每个测试用例，调用 `book` 函数最多不超过 `400`次

## 我的解法

未解出！有与线段树相似的想法，但是未写出！

## 其他解法

### 差分数组

```c++
class MyCalendarThree {
public:
    MyCalendarThree() {
        
    }
    
    int book(int start, int end) {
        int ans = 0;
        int maxBook = 0;
        cnt[start]++;
        cnt[end]--;
        for (auto &[_, freq] : cnt) {
            maxBook += freq;
            ans = max(maxBook, ans);
        }
        return ans;
    }
private:
    map<int, int> cnt;
};
```

```java
class MyCalendarThree {
    private TreeMap<Integer, Integer> cnt;

    public MyCalendarThree() {
        cnt = new TreeMap<Integer, Integer>();
    }
    
    public int book(int start, int end) {
        int ans = 0;
        int maxBook = 0;
        cnt.put(start, cnt.getOrDefault(start, 0) + 1);
        cnt.put(end, cnt.getOrDefault(end, 0) - 1);
        for (Map.Entry<Integer, Integer> entry : cnt.entrySet()) {
            int freq = entry.getValue();
            maxBook += freq;
            ans = Math.max(maxBook, ans);
        }
        return ans;
    }
}
```

```python
from sortedcontainers import SortedDict

class MyCalendarThree:
    def __init__(self):
        self.d = SortedDict()

    def book(self, start: int, end: int) -> int:
        self.d[start] = self.d.setdefault(start, 0) + 1
        self.d[end] = self.d.setdefault(end, 0) - 1

        ans = maxBook = 0
        for freq in self.d.values():
            maxBook += freq
            ans = max(ans, maxBook)
        return ans
```

#### 思路与算法

可以参考 TOP_731. 我的日程安排表 II 的解法二，我们可以采用同样的思路即可。利用差分数组的思想，每当我们预定一个新的日程安排 $[\textit{start}, \textit{end})$，在 $\textit{start}$计数 $\textit{cnt}[\textit{start}]$ 加 1，表示从 $\textit{start}$预定的数目加 1；从 $\textit{end}$计数 $\textit{cnt}[\textit{end}]$减 1，表示从 $\textit{end}$开始预定的数目减 1。此时以起点 x 开始的预定的数目 $\textit{book}_x = \sum_{y \le x}\textit{cnt}[y]$，我们对计数进行累加依次求出最大的预定数目即可。由于本题中$ \textit{start}, \textit{end}$数量较大，我们利用 $\texttt{TreeMap}$计数即可。

#### 复杂度分析

- 时间复杂度：$O(n^2)$，其中 n 为日程安排的数量。每次求的最大的预定需要遍历所有的日程安排。

- 空间复杂度：O(n)，其中 n 为日程安排的数量。需要空间存储所有的日程安排计数，需要的空间为 O(n)。


### 线段树

```c++
class MyCalendarThree {
public:
    unordered_map<int, pair<int, int>> tree;

    MyCalendarThree() {

    }
    
    void update(int start, int end, int l, int r, int idx) {
        if (r < start || end < l) {
            return;
        } 
        if (start <= l && r <= end) {
            tree[idx].first++;
            tree[idx].second++;
        } else {
            int mid = (l + r) >> 1;
            update(start, end, l, mid, 2 * idx);
            update(start, end, mid + 1, r, 2 * idx + 1);
            tree[idx].first = tree[idx].second + max(tree[2 * idx].first, tree[2 * idx + 1].first);
        }
    }

    int book(int start, int end) {            
        update(start, end - 1, 0, 1e9, 1);
        return tree[1].first;
    }
};
```

```java
class MyCalendarThree {
    private Map<Integer, Integer> tree;
    private Map<Integer, Integer> lazy;

    public MyCalendarThree() {
        tree = new HashMap<Integer, Integer>();
        lazy = new HashMap<Integer, Integer>();
    }
    
    public int book(int start, int end) {
        update(start, end - 1, 0, 1000000000, 1);
        return tree.getOrDefault(1, 0);
    }

    public void update(int start, int end, int l, int r, int idx) {
        if (r < start || end < l) {
            return;
        } 
        if (start <= l && r <= end) {
            tree.put(idx, tree.getOrDefault(idx, 0) + 1);
            lazy.put(idx, lazy.getOrDefault(idx, 0) + 1);
        } else {
            int mid = (l + r) >> 1;
            update(start, end, l, mid, 2 * idx);
            update(start, end, mid + 1, r, 2 * idx + 1);
            tree.put(idx, lazy.getOrDefault(idx, 0) + Math.max(tree.getOrDefault(2 * idx, 0), tree.getOrDefault(2 * idx + 1, 0)));
        }
    }
}
```

```python
class MyCalendarThree:
    def __init__(self):
        self.tree = defaultdict(int)
        self.lazy = defaultdict(int)

    def update(self, start: int, end: int, l: int, r: int, idx: int):
        if r < start or end < l:
            return
        if start <= l and r <= end:
            self.tree[idx] += 1
            self.lazy[idx] += 1
        else:
            mid = (l + r) // 2
            self.update(start, end, l, mid, idx * 2)
            self.update(start, end, mid + 1, r, idx * 2 + 1)
            self.tree[idx] = self.lazy[idx] + max(self.tree[idx * 2], self.tree[idx * 2 + 1])

    def book(self, start: int, end: int) -> int:
        self.update(start, end - 1, 0, 10 ** 9, 1)
        return self.tree[1]
```

#### 思路与算法

利用线段树，假设我们开辟了数组 $\textit{arr}[0,\cdots, 10^9]$，初始时每个元素的值都为 0，对于每次行程预定的区间$ [\textit{start}, \textit{end})$ ，则我们将区间中的元素 $\textit{arr}[\textit{start},\cdots,\textit{end}-1]$ 中的每个元素加 1，最终只需要求出数组 arr 的最大元素即可。实际我们不必实际开辟数组 $\textit{arr}$，可采用动态线段树，懒标记 $\textit{lazy}$ 标记区间 $[l,r]$ 进行累加的次数，$\textit{tree}$记录区间 $[l,r]$ 的最大值，最终返回区间 $[0,10^9]$ 中的最大元素即可。

#### 复杂度分析

- 时间复杂度：$O(n \log C)$，其中 n 为日程安排的数量。由于使用了线段树查询，线段树的最大深度为 $\log C$, 每次最多会查询 $\log C$ 个节点，每次求最大的预定需的时间复杂度为 $O(\log C + \log C)$，因此时间复杂度为 $O(n \log C)$，在此 C 取固定值即为 $10^9$。

- 空间复杂度：$O(n \log C)$，其中 n 为日程安排的数量。由于该解法采用的为动态线段树，线段树的最大深度为 $\log C$，每次预定最多会在线段树上增加 $\log C$ 个节点，因此空间复杂度为 $O(n \log C)$，在此 C 取固定值即为 $10^9$。


