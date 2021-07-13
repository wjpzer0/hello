# 题目

给定一位研究者论文被引用次数的数组（被引用次数是非负整数）。编写一个方法，计算出研究者的 *h* 指数。

[h 指数的定义](https://baike.baidu.com/item/h-index/3991452?fr=aladdin)：h 代表“高引用次数”（high citations），一名科研人员的 h 指数是指他（她）的 （N 篇论文中）**总共**有 h 篇论文分别被引用了**至少** h 次。且其余的 *N - h* 篇论文每篇被引用次数 **不超过** *h* 次。

例如：某人的 h 指数是 20，这表示他已发表的论文中，每篇被引用了至少 20 次的论文总共有 20 篇。

**示例：**

```
输入：citations = [3,0,6,1,5]
输出：3 
解释：给定数组表示研究者总共有 5 篇论文，每篇论文相应的被引用了 3, 0, 6, 1, 5 次。
     由于研究者有 3 篇论文每篇 至少 被引用了 3 次，其余两篇论文每篇被引用 不多于 3 次，所以她的 h 指数是 3。
```

**提示：**如果 *h* 有多种可能的值，*h* 指数是其中最大的那个。

## 我的解法

未解出，想到了二分法，但是无法解决判断加减的条件！

查看题解后写出

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        l = 0
        r = len(citations)+1

        while l < r:
            mid = l + (r - l) // 2
            cnt = 0
            for i in citations:
                if i >= mid:
                    cnt += 1
            if cnt < mid:
                r = mid
            else:
                l = mid + 1

        return l-1
```

## 其他解法

### 排序

```c++
class Solution {
public:
    int hIndex(vector<int>& citations) {
        sort(citations.begin(), citations.end());
        int h = 0, i = citations.size() - 1;
        while (i >= 0 && citations[i] > h) {
            h++;
            i--;
        }
        return h;
    }
};
```

```java
class Solution {
    public int hIndex(int[] citations) {
        Arrays.sort(citations);
        int h = 0, i = citations.length - 1; 
        while (i >= 0 && citations[i] > h) {
            h++; 
            i--;
        }
        return h;
    }
}
```

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        sorted_citation = sorted(citations, reverse = True)
        h = 0; i = 0; n = len(citations)
        while i < n and sorted_citation[i] > h:
            h += 1
            i += 1
        return h
```

首先我们可以将初始的 $\text{H}$ 指数 h 设为 0，然后将引用次数排序，并且对排序后的数组从大到小遍历。

根据 $\text{H}$ 指数的定义，如果当前 $\text{H}$ 指数为 h 并且在遍历过程中找到当前值 $\textit{citations}[i] > h$，则说明我们找到了一篇被引用了至少 h+1 次的论文，所以将现有的 h 值加 1。继续遍历直到 h 无法继续增大。最后返回 h 作为最终答案。

#### 复杂度分析

时间复杂度：$O(n \log n)$，其中 n 为数组 $\textit{citations}$ 的长度。即为排序的时间复杂度。

空间复杂度：$O(\log n)$，其中 n 为数组 $\textit{citations}$ 的长度。即为排序的空间复杂度。

### 计数排序

```c++
class Solution {
public:
    int hIndex(vector<int>& citations) {
        int n = citations.size(), tot = 0;
        vector<int> counter(n + 1);
        for (int i = 0; i < n; i++) {
            if (citations[i] >= n) {
                counter[n]++;
            } else {
                counter[citations[i]]++;
            }
        }
        for (int i = n; i >= 0; i--) {
            tot += counter[i];
            if (tot >= i) {
                return i;
            }
        }
        return 0;
    }
};
```

```java
public class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length, tot = 0;
        int[] counter = new int[n + 1];
        for (int i = 0; i < n; i++) {
            if (citations[i] >= n) {
                counter[n]++;
            } else {
                counter[citations[i]]++;
            }
        }
        for (int i = n; i >= 0; i--) {
            tot += counter[i];
            if (tot >= i) {
                return i;
            }
        }
        return 0;
    }
}
```

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        n = len(citations); tot = 0
        counter = [0] * (n+1)
        for c in citations:
            if c >= n:
                counter[n] += 1
            else:
                counter[c] += 1
        for i in range(n, -1, -1):
            tot += counter[i]
            if tot >= i:
                return i
        return 0
```

根据上述解法我们发现，最终的时间复杂度与排序算法的时间复杂度有关，所以我们可以使用计数排序算法，新建并维护一个数组 $\textit{counter}$ 用来记录当前引用次数的论文有几篇。

根据定义，我们可以发现 $\text{H}$ 指数不可能大于总的论文发表数，所以对于引用次数超过论文发表数的情况，我们可以将其按照总的论文发表数来计算即可。这样我们可以限制参与排序的数的大小为$ [0,n]$（其中 n 为总的论文发表数），使得计数排序的时间复杂度降低到 O(n)。

最后我们可以从后向前遍历数组 $\textit{counter}$，对于每个 $0 \le i \le n$，在数组 $\textit{counter}$ 中得到大于或等于当前引用次数 i 的总论文数。当我们找到一个 $\text{H}$ 指数时跳出循环，并返回结果。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为数组 $\textit{citations}$ 的长度。需要遍历数组 $\textit{citations}$ 一次，以及遍历长度为 n+1 的数组 $\textit{counter}$ 一次。

- 空间复杂度：O(n)，其中 n 为数组 $\textit{citations}$ 的长度。需要创建长度为 n+1 的数组 $\textit{counter}$。

