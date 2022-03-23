# 题目

给定整数 `n` 和 `k`，返回 `[1, n]` 中字典序第 `k` 小的数字。

**示例 1:**

```
输入: n = 13, k = 2
输出: 10
解释: 字典序的排列是 [1, 10, 11, 12, 13, 2, 3, 4, 5, 6, 7, 8, 9]，所以第二小的数字是 10。
```

**示例 2:**

```
输入: n = 1, k = 1
输出: 1
```

**提示:**

- $1 <= k <= n <= 10^9$

## 我的解法

超时，利用了基数排序的方法写出！想到过用字典树，但是没有想到如何让在字典树中找到第k个数字！

```python
class Solution:
    def findKthNumber(self, n: int, k: int) -> int:
        nl = len(str(n))
        s = deque([str(i) for i in range(1, n + 1)])
        dic = defaultdict(deque)
        for i in range(nl - 1, -1, -1):
            while s:
                num = s.popleft()
                if i >= len(num):
                    dic['0'].append(num)
                else:
                    dic[num[i]].append(num)
            print(dic)
            for j in range(10):
                mid = str(j)
                dq = dic[mid]
                while dq:
                    s.append(dq.popleft())
        return int(s[k - 1])
```

## 其他解法

### 字典树思想

```c++
class Solution {
public:
    int getSteps(int curr, long n) {
        int steps = 0;
        long first = curr;
        long last = curr;
        while (first <= n) {
            steps += min(last, n) - first + 1;
            first = first * 10;
            last = last * 10 + 9;
        }
        return steps;
    }

    int findKthNumber(int n, int k) {
        int curr = 1;
        k--;
        while (k > 0) {
            int steps = getSteps(curr, n);
            if (steps <= k) {
                k -= steps;
                curr++;
            } else {
                curr = curr*10;
                k--;
            }
        }
        return curr;
    }
};
```

```java
class Solution {
    public int findKthNumber(int n, int k) {
        int curr = 1;
        k--;
        while (k > 0) {
            int steps = getSteps(curr, n);
            if (steps <= k) {
                k -= steps;
                curr++;
            } else {
                curr = curr * 10;
                k--;
            }
        }
        return curr;
    }

    public int getSteps(int curr, long n) {
        int steps = 0;
        long first = curr;
        long last = curr;
        while (first <= n) {
            steps += Math.min(last, n) - first + 1;
            first = first * 10;
            last = last * 10 + 9;
        }
        return steps;
    }
}
```

```python
class Solution:
    def getSteps(self, cur: int, n: int) -> int:
        steps, first, last = 0, cur, cur
        while first <= n:
            steps += min(last, n) - first + 1
            first *= 10
            last = last * 10 + 9
        return steps

    def findKthNumber(self, n: int, k: int) -> int:
        cur = 1
        k -= 1
        while k:
            steps = self.getSteps(cur, n)
            if steps <= k:
                k -= steps
                cur += 1
            else:
                cur *= 10
                k -= 1
        return cur
```

#### 思路

题目要求找到字典序第 k 小的数字，可以将所有的数字都转换成字符串，然后排序找到第 k 小的数字即可，但显然时间复杂度不符合要求。我们利用字典树的特性将所有小于等于 n 的数字按照字典序的方式进行重建，可以得到如下：

![1](https://assets.leetcode-cn.com/solution-static/440/440_1.PNG)

通过观察可以发现，前序遍历该字典树即可得到字典序从小到大的数字序列，遍历到第 k 个节点即为第 k 小的数字。我们可以构造字典树，并通过前序遍历求得目标节点，时间复杂度为 O(k)。实际不需要构造字典树，已知节点 i 的子节点为$ (10 \times i, 10 \times i + 1, \cdots, 10 \times i + 9)$，可以通过计算找到前序遍历第 k 个节点即可。设当前的字典树的第 i 小的节点为 $n_i$，则只需按照先序遍历再继续往后找 k - i 个节点即为目标节点。设与 $n_i$右侧相邻的兄弟节点为 $n_{i+1}$，按照先序遍历的顺序，先访问 $n_i$构造的子树，再访问 $n_{i+1}$，此时满足 $n_i < n_{i+1}$。设以 $n_{i}$为根节点构成的子树的节点数目为 $\textit{step}(n_i)$，则此时有以下两种可能：

- 如果满足 $\textit{step}(n_i) \le k - i$，则此时可以肯定第 k 小的节点一定不在 $n_i$为根的子树中，存在于 $n_i$的兄弟节点构成的子树中，则此时可以跳过 $\textit{step}(n_i)$ 个节点，从兄弟节点 $n_{i+1}$开始往后查找 $k - i - \textit{step}(n_i)$个节点。
- 如果满足 $\textit{step}(n_i) > k - i$，则此时可以肯定第 k 小的节点一定在 $n_i$构成的子树中，则此时需要在 $n_i$的孩子节点中依次进行查找。此时跳过子树中最小的根节点 $n_i$，从左侧第一个孩子开始往后查找 k - i - 1 个节点，此时左侧第一个孩子的值为 $10 \times n_i$。
- 依次重复上述操作直到找到 k 小的节点即可。


在这里比较难以处理的是如何计算 $\textit{step}(n_i)$，即找到以 $n_i$为根的子树下有多少节点。可以按照层次遍历子树，$\textit{first}_i$指向第 i 层的最左侧的孩子节点， $\textit{last}_i$指向第 i 层的最右侧的孩子节点，根据推理可以知道: $\textit{first}_i = 10 \times \textit{first}_{i-1}$，$\textit{last}_i = 10 \times \textit{last}_{i-1} + 9$，第 i 层共有 ${last}_i - \textit{first}_i + 1$ 个节点，由于所有的节点都需要满足小于等于 n，所以第 i 层的最右侧节点应该为 $\min(n,{\textit{last}}_i)$，不断迭代直到 $\textit{first}_i > n$ 则终止向下搜索。实际上可以观察到最终结果一定是沿着字典树从根节点到某个叶子节点的路径进行搜索。

#### 复杂度分析

- 时间复杂度：$O(\log^2 n)$，其中 n 为 给定的 数值的大小。每次计算子树下的节点数目的搜索深度最大为 $\log_{10}n$，最多需要搜索 $\log_{10}n$层，每一层最多需要计算 10 次，最多需要计算 $10 \times (\log_{10}n)^2$次，因此时间复杂度为 $O(\log^2 n)$。
- 空间复杂度：O(1)，不需要开辟额外的空间，只需常数空间记录常量即可。


