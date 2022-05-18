# 题目

几乎每一个人都用 [乘法表](https://baike.baidu.com/item/乘法表)。但是你能在乘法表中快速找到第`k`小的数字吗？

给定高度`m` 、宽度`n` 的一张 `m * n`的乘法表，以及正整数`k`，你需要返回表中第`k` 小的数字。

**例 1：**

```
输入: m = 3, n = 3, k = 5
输出: 3
解释: 
乘法表:
1	2	3
2	4	6
3	6	9

第5小的数字是 3 (1, 2, 2, 3, 3).
```

**例 2：**

```
输入: m = 2, n = 3, k = 6
输出: 6
解释: 
乘法表:
1	2	3
2	4	6

第6小的数字是 6 (1, 2, 2, 3, 4, 6).
```

**注意：**

1. `m` 和 `n` 的范围在 [1, 30000] 之间。
2. `k` 的范围在 [1, m * n] 之间。

## 我的解法

废物一个，划水了！

查看大佬的解法！

![668. 乘法表中第k小的数.002.png](https://pic.leetcode-cn.com/1652856491-uMHrYH-668.%20%E4%B9%98%E6%B3%95%E8%A1%A8%E4%B8%AD%E7%AC%ACk%E5%B0%8F%E7%9A%84%E6%95%B0.002.png)

![img](https://pic.leetcode-cn.com/1652856957-NescwS-668.%20%E4%B9%98%E6%B3%95%E8%A1%A8%E4%B8%AD%E7%AC%ACk%E5%B0%8F%E7%9A%84%E6%95%B0.004.png)

![img](https://pic.leetcode-cn.com/1652856957-DTzzUp-668.%20%E4%B9%98%E6%B3%95%E8%A1%A8%E4%B8%AD%E7%AC%ACk%E5%B0%8F%E7%9A%84%E6%95%B0.005.png)

![img](https://pic.leetcode-cn.com/1652856957-pcPjfR-668.%20%E4%B9%98%E6%B3%95%E8%A1%A8%E4%B8%AD%E7%AC%ACk%E5%B0%8F%E7%9A%84%E6%95%B0.006.png)

![img](https://pic.leetcode-cn.com/1652856957-tBNUks-668.%20%E4%B9%98%E6%B3%95%E8%A1%A8%E4%B8%AD%E7%AC%ACk%E5%B0%8F%E7%9A%84%E6%95%B0.007.png)

```python
class Solution:
    def findKthNumber(self, m: int, n: int, k: int) -> int:
        left = 1
        right = m * n 
        while left < right:
            x = left + (right - left) // 2
            count = 0
            for i in range(1, m + 1):
                count += min(x // i, n)
            if count >= k:
                right = x
            else:
                left = x + 1
        return left
```

## 其他解法

### 二分查找

```c++
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int left = 1, right = m * n;
        while (left < right) {
            int x = left + (right - left) / 2;
            int count = x / n * n;
            for (int i = x / n + 1; i <= m; ++i) {
                count += x / i;
            }
            if (count >= k) {
                right = x;
            } else {
                left = x + 1;
            }
        }
        return left;
    }
};
```

```java
class Solution {
    public int findKthNumber(int m, int n, int k) {
        int left = 1, right = m * n;
        while (left < right) {
            int x = left + (right - left) / 2;
            int count = x / n * n;
            for (int i = x / n + 1; i <= m; ++i) {
                count += x / i;
            }
            if (count >= k) {
                right = x;
            } else {
                left = x + 1;
            }
        }
        return left;
    }
}
```

```python
class Solution:
    def findKthNumber(self, m: int, n: int, k: int) -> int:
        return bisect_left(range(m * n), k, key=lambda x: x // n * n + sum(x // i for i in range(x // n + 1, m + 1)))
```

由于 m 和 n 很大，直接求出所有数字然后找到第 k 小会超出时间限制。不妨考虑一个反向问题：对于乘法表中的数字 x，它是乘法表中第几小的数字？

求第几小等价于求有多少数字不超过 x。我们可以遍历乘法表的每一行，对于乘法表的第 i 行，其数字均为 i 的倍数，因此不超过 x 的数字有 $\min(\Big\lfloor\dfrac{x}{i}\Big\rfloor,n)$个，所以整个乘法表不超过 x 的数字个数为
$$
\sum_{i=1}^{m} \min(\Big\lfloor\dfrac{x}{i}\Big\rfloor,n
$$
由于 $i\le \Big\lfloor\dfrac{x}{n}\Big\rfloor$ 时 $\Big\lfloor\dfrac{x}{i}\Big\rfloor \ge n$，上式可化简为
$$
\Big\lfloor\dfrac{x}{n}\Big\rfloor\cdot n + \sum_{i=\Big\lfloor\dfrac{x}{n}\Big\rfloor+1}^{m} \Big\lfloor\dfrac{x}{i}\Big\rfloor
$$
由于 xx 越大上式越大，x 越小上式越小，因此我们可以二分 x 找到答案，二分的初始边界为乘法表的元素范围，即 $[1,mn]$。

#### 复杂度分析

- 时间复杂度：$O(m\log mn)$。二分的次数为 $O(\log mn)$，每次二分需要 O(m) 的时间计算。

- 空间复杂度：O(1)。只需要常数的空间存放若干变量。


