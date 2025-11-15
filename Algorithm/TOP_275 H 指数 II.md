# 题目

给定一位研究者论文被引用次数的数组（被引用次数是非负整数），数组已经按照 **升序排列** 。编写一个方法，计算出研究者的 *h* 指数。

h 指数的定义: “h 代表“高引用次数”（high citations），一名科研人员的 h 指数是指他（她）的 （N 篇论文中）**总共**有 h 篇论文分别被引用了**至少** h 次。（其余的 *N - h* 篇论文每篇被引用次数**不多于** *h* 次。）"

**示例:**

```
输入: citations = [0,1,3,5,6]
输出: 3 
解释: 给定数组表示研究者总共有 5 篇论文，每篇论文相应的被引用了 0, 1, 3, 5, 6 次。
     由于研究者有 3 篇论文每篇至少被引用了 3 次，其余两篇论文每篇被引用不多于 3 次，所以她的 h 指数是 3。
```

**说明:**

如果 *h* 有多有种可能的值 ，*h* 指数是其中最大的那个。

**进阶：**

- 这是 H 指数 的延伸题目，本题中的 `citations` 数组是保证有序的。
- 你可以优化你的算法到对数时间复杂度吗？

## 我的解法

未解出，题目还是没有理清含义！

### 理解题意

这道问题理解题意要花很长时间，一个有效的办法就是仔细研究示例，然后去理解题目的意思。我真正明白题目的意思是看到这句描述：

> 例如：某人的 h 指数是 20，这表示他已发表的论文中，每篇被引用了至少 20 次的论文总共有 20 篇。
>

所以 h 指数是 20 表示：引用次数大于等于 20 的文章数量最少是 20 篇。

再来理解一下题目中给出的定义：

> N 篇论文中总共有 h 篇论文分别被引用了至少 h 次；
> 其余的 N - h 篇论文每篇被引用次数不超过 h 次。

h 指数想说的是这样一件事情，一个人的论文根据被引用的次数，有一个阈值（分水岭，就是这里的 h），引用次数大于等于这个阈值的论文是「高引用论文」。所以可以把一个研究者的论文被引用的次数 按照升序排序（这一点是题目给出的前提，很重要）。题目其实要我们找的是一条分割线，这条分割线的含义是：分割线右边的所有论文的引用次数都很高。重要的事情说 3 遍：

**h 指数是 论文数量，不是引用次数。**
**h 指数是 论文数量，不是引用次数。**
**h 指数是 论文数量，不是引用次数。**

所以分割线满足的条件是：分割线右边的最少引用次数 >= 分割线右边的论文篇数。题目要求返回的是论文数量。

再看看题目的示例：

![image.png](https://pic.leetcode-cn.com/1625998400-VBXQNr-image.png)

这个例子有点儿特殊，论文被引用了 3 次，篇数有 3 篇。再来看一个更一般的例子：

![image.png](https://pic.leetcode-cn.com/1625998502-XMZfqD-image.png)

结论：这条分割线越靠左边，说明被引用的次数很多，文章还很多，h 指数越高。

在有序数组中查找一个位置，可以使用二分查找。

## 其他解法

### 二分查找

```c++
class Solution {
public:
    int hIndex(vector<int>& citations) {
        int n = citations.size();
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (citations[mid] >= n - mid) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return n - left;
    }
};
```

```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int left = 0, right = n - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (citations[mid] >= n - mid) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return n - left;
    }
}
```

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        n = len(citations)
        left = 0; right = n - 1
        while left <= right:
            mid = left + (right - left) // 2
            if citations[mid] >= n - mid:
                right = mid - 1
            else:
                left = mid + 1
        return n - left
```

由于数组 $\textit{citations}$ 已经按照升序排序，因此可以使用二分查找。

设查找范围的初始左边界 $\textit{left}$ 为 0, 初始右边界 $\textit{right}$ 为 n-1，其中 n 为数组 $\textit{citations}$的长度。每次在查找范围内取中点 $\textit{mid}$，则有 $n-\textit{mid}$ 篇论文被引用了至少 $\textit{citations}[\textit{mid}]$次。如果在查找过程中满足$ \textit{citations}[\textit{mid}] \ge n - \textit{mid}$，则移动右边界 $\textit{right}$，否则移动左边界 $\textit{left}$。

#### 复杂度分析

- 时间复杂度：$O(\log n)$，其中 n 为数组 $\textit{citations}$ 的长度。二分查找的时间复杂度为 $O(\log n)$。

- 空间复杂度：O(1)。


