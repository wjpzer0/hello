# 题目

实现一个 `MyCalendar` 类来存放你的日程安排。如果要添加的日程安排不会造成 **重复预订** ，则可以存储这个新的日程安排。

当两个日程安排有一些时间上的交叉时（例如两个日程安排都在同一时间内），就会产生 **重复预订** 。

日程可以用一对整数 `start` 和 `end` 表示，这里的时间是半开区间，即 `[start, end)`, 实数 `x` 的范围为，  `start <= x < end` 。

实现 `MyCalendar` 类：

- `MyCalendar()` 初始化日历对象。
- `boolean book(int start, int end)` 如果可以将日程安排成功添加到日历中而不会导致重复预订，返回 `true` 。否则，返回 `false` 并且不要将该日程安排添加到日历中。

**示例：**

```
输入：
["MyCalendar", "book", "book", "book"]
[[], [10, 20], [15, 25], [20, 30]]
输出：
[null, true, false, true]

解释：
MyCalendar myCalendar = new MyCalendar();
myCalendar.book(10, 20); // return True
myCalendar.book(15, 25); // return False ，这个日程安排不能添加到日历中，因为时间 15 已经被另一个日程安排预订了。
myCalendar.book(20, 30); // return True ，这个日程安排可以添加到日历中，因为第一个日程安排预订的每个时间都小于 20 ，且不包含时间 20 。
```

**提示：**

- 0 <= start < end <= 109
- 每个测试用例，调用 `book` 方法的次数最多不超过 `1000` 次。

## 我的解法

有思路，但是部分容器包记不到！查看题解后写出！

```python
from sortedcontainers import SortedDict

class MyCalendar:

    def __init__(self):
        self.map = SortedDict()

    def book(self, start: int, end: int) -> bool:
        point = self.map.bisect_left(end)
        if point == 0 or self.map.items()[point - 1][1] <= start:
            self.map[start] = end
            return True
        return False

# Your MyCalendar object will be instantiated and called as such:
# obj = MyCalendar()
# param_1 = obj.book(start,end)
```

## 其他解法

### 直接遍历

```c++
class MyCalendar {
    vector<pair<int, int>> booked;

public:
    bool book(int start, int end) {
        for (auto &[l, r] : booked) {
            if (l < end && start < r) {
                return false;
            }
        }
        booked.emplace_back(start, end);
        return true;
    }
};
```

```java
class MyCalendar {
    List<int[]> booked;

    public MyCalendar() {
        booked = new ArrayList<int[]>();
    }

    public boolean book(int start, int end) {
        for (int[] arr : booked) {
            int l = arr[0], r = arr[1];
            if (l < end && start < r) {
                return false;
            }
        }
        booked.add(new int[]{start, end});
        return true;
    }
}
```

```python
class MyCalendar:
    def __init__(self):
        self.booked = []

    def book(self, start: int, end: int) -> bool:
        if any(l < end and start < r for l, r in self.booked):
            return False
        self.booked.append((start, end))
        return True
```

我们记录下所有已经预订的课程安排区间，当我们预订新的区间 $[\textit{start}, \textit{end})$时，此时检查当前已经预订的每个日程安排是否与新日程安排冲突。若不冲突，则可以添加新的日程安排。

对于两个区间$ [s_1, e_1)$ 和$ [s_2, e_2)$，如果二者没有交集，则此时应当满足 $s_1 \ge e_2$或者 $s_2 \ge e_1$，这就意味着如果满足 $s_1 < e_2$并且 $s_2 < e_1$，则两者会产生交集。

#### 复杂度分析

- 时间复杂度：$O(n^2)$, 其中 n 表示日程安排的数量。由于每次在进行预订时，都需要遍历所有已经预订的行程安排。

- 空间复杂度：O(n)，其中 n 表示日程安排的数量。需要保存所有已经预订的行程。


### 二分查找

```c++
class MyCalendar {
    set<pair<int, int>> booked;

public:
    bool book(int start, int end) {
        auto it = booked.lower_bound({end, 0});
        if (it == booked.begin() || (--it)->second <= start) {
            booked.emplace(start, end);
            return true;
        }
        return false;
    }
};
```

```java
class MyCalendar {
    TreeSet<int[]> booked;

    public MyCalendar() {
        booked = new TreeSet<int[]>((a, b) -> a[0] - b[0]);
    }

    public boolean book(int start, int end) {
        if (booked.isEmpty()) {
            booked.add(new int[]{start, end});
            return true;
        }
        int[] tmp = {end, 0};
        int[] arr = booked.ceiling(tmp);
        int[] prev = arr == null ? booked.last() : booked.lower(arr);
        if (arr == booked.first() || booked.lower(tmp)[1] <= start) {
            booked.add(new int[]{start, end});
            return true;
        }
        return false;
    }
}
```

```python
from sortedcontainers import SortedDict

class MyCalendar:
    def __init__(self):
        self.booked = SortedDict()

    def book(self, start: int, end: int) -> bool:
        i = self.booked.bisect_left(end)
        if i == 0 or self.booked.items()[i - 1][1] <= start:
            self.booked[start] = end
            return True
        return False
```

如果我们按时间顺序维护日程安排，则可以通过二分查找日程安排的情况来检查新日程安排是否可以预订，若可以预订则在排序结构中更新插入日程安排。

- 需要一个数据结构能够保持元素排序和支持快速插入，可以用$ \texttt{TreeSet}$来构建。对于给定的区间 $[\textit{start}, \textit{end})$，我们每次查找起点大于等于 $\textit{end}$的第一个区间 $[l_1,r_1)$，同时紧挨着 $[l_1,r_1)$ 的前一个区间为 $[l_2,r_2)$，此时如果满足 $r_2 \le \textit{start} < \textit{end} \le l_1$，则该区间可以预订。

#### 复杂度分析

- 时间复杂度：$O(n \log n)$, 其中 n 表示日程安排的数量。由于每次在进行预订时，都需要进行二分查找，需要的时间为 $O(\log n)$。

- 空间复杂度：O(n)，其中n 表示日程安排的数量。需要保存所有已经预订的行程。


### 线段树

```c++
class MyCalendar {
    unordered_set<int> tree, lazy;

public:
    bool query(int start, int end, int l, int r, int idx) {
        if (r < start || end < l) {
            return false;
        }
        /* 如果该区间已被预订，则直接返回 */
        if (lazy.count(idx)) {
            return true;
        }
        if (start <= l && r <= end) {
            return tree.count(idx);
        }
        int mid = (l + r) >> 1;
        return query(start, end, l, mid, 2 * idx) ||
               query(start, end, mid + 1, r, 2 * idx + 1);
    }

    void update(int start, int end, int l, int r, int idx) {
        if (r < start || end < l) {
            return;
        }
        if (start <= l && r <= end) {
            tree.emplace(idx);
            lazy.emplace(idx);
        } else {
            int mid = (l + r) >> 1;
            update(start, end, l, mid, 2 * idx);
            update(start, end, mid + 1, r, 2 * idx + 1);
            tree.emplace(idx);
            if (lazy.count(2 * idx) && lazy.count(2 * idx + 1)) {
                lazy.emplace(idx);
            }
        }
    }

    bool book(int start, int end) {
        if (query(start, end - 1, 0, 1e9, 1)) {
            return false;
        }
        update(start, end - 1, 0, 1e9, 1);
        return true;
    }
};
```

```java
class MyCalendar {
    Set<Integer> tree;
    Set<Integer> lazy;

    public MyCalendar() {
        tree = new HashSet<Integer>();
        lazy = new HashSet<Integer>();
    }

    public boolean book(int start, int end) {
        if (query(start, end - 1, 0, 1000000000, 1)) {
            return false;
        }
        update(start, end - 1, 0, 1000000000, 1);
        return true;
    }

    public boolean query(int start, int end, int l, int r, int idx) {
        if (start > r || end < l) {
            return false;
        }
        /* 如果该区间已被预订，则直接返回 */
        if (lazy.contains(idx)) {
            return true;
        }
        if (start <= l && r <= end) {
            return tree.contains(idx);
        } else {
            int mid = (l + r) >> 1;
            if (end <= mid) {
                return query(start, end, l, mid, 2 * idx);
            } else if (start > mid) {
                return query(start, end, mid + 1, r, 2 * idx + 1);
            } else {
                return query(start, end, l, mid, 2 * idx) | query(start, end, mid + 1, r, 2 * idx + 1);
            }
        }
    }

    public void update(int start, int end, int l, int r, int idx) {
        if (r < start || end < l) {
            return;
        } 
        if (start <= l && r <= end) {
            tree.add(idx);
            lazy.add(idx);
        } else {
            int mid = (l + r) >> 1;
            update(start, end, l, mid, 2 * idx);
            update(start, end, mid + 1, r, 2 * idx + 1);
            tree.add(idx);
        }
    }
}
```

```python
class MyCalendar:
    def __init__(self):
        self.tree = set()
        self.lazy = set()

    def query(self, start: int, end: int, l: int, r: int, idx: int) -> bool:
        if r < start or end < l:
            return False
        if idx in self.lazy:  # 如果该区间已被预订，则直接返回
            return True
        if start <= l and r <= end:
            return idx in self.tree
        mid = (l + r) // 2
        return self.query(start, end, l, mid, 2 * idx) or \
               self.query(start, end, mid + 1, r, 2 * idx + 1)

    def update(self, start: int, end: int, l: int, r: int, idx: int) -> None:
        if r < start or end < l:
            return
        if start <= l and r <= end:
            self.tree.add(idx)
            self.lazy.add(idx)
        else:
            mid = (l + r) // 2
            self.update(start, end, l, mid, 2 * idx)
            self.update(start, end, mid + 1, r, 2 * idx + 1)
            self.tree.add(idx)
            if 2 * idx in self.lazy and 2 * idx + 1 in self.lazy:
                self.lazy.add(idx)

    def book(self, start: int, end: int) -> bool:
        if self.query(start, end - 1, 0, 10 ** 9, 1):
            return False
        self.update(start, end - 1, 0, 10 ** 9, 1)
        return True
```

利用线段树，假设我们开辟了数组 $\textit{arr}[0,\cdots, 10^9]$，初始时每个元素的值都为 0，对于每次行程预订的区间 $[\textit{start}, \textit{end})$ ，则我们将区间中的元素 $\textit{arr}[\textit{start},\cdots,\textit{end}-1]$ 中的每个元素都标记为 1，每次调用 $\texttt{book}$时，我们只需要检测 $\textit{arr}[\textit{start},\cdots,\textit{end}-1]$ 区间内是否有元素被标记为 1。实际我们不必实际开辟数组 $\textit{arr}$，可采用动态线段树，懒标记 $\textit{lazy}$标记区间$ [l,r]$ 已经被预订，$\textit{tree}$记录区间 $[l,r]$ 的是否存在标记为 1 的元素。

每次进行 $\texttt{book}$操作时，首先判断区间 $[\textit{start},\cdots,\textit{end}-1]$ 是否存在元素被标记，如果存在被标记为 1 的元素，则表明该区间不可预订；否则，则将可以预订。预订完成后，将$ \textit{arr}[\textit{start},\cdots,\textit{end}-1]$ 进行标记为 1，并同时更新线段树。

#### 复杂度分析

- 时间复杂度：$O(n \log C)$，其中 n 为日程安排的数量。由于使用了线段树查询，线段树的最大深度为 $\log C$，每次最多会查询 $\log C$ 个节点，每次求最大的预订需的时间复杂度为 $O(\log C + \log C)$，因此时间复杂度为 $O(n \log C)$，在此 C 取固定值 $10^9$。

- 空间复杂度：$O(n \log C)$，其中 n 为日程安排的数量。由于该解法采用的为动态线段树，线段树的最大深度为 $\log C$，每次预订最多会在线段树上增加 $\log C$ 个节点，因此空间复杂度为 $O(n \log C)$，在此 C 取固定值 $10^9$。


