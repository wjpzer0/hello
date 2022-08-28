# 题目

 `f(x)` 是 `x!` 末尾是 0 的数量。回想一下 `x! = 1 * 2 * 3 * ... * x`，且 `0! = 1` 。

- 例如， `f(3) = 0` ，因为 `3! = 6` 的末尾没有 0 ；而 `f(11) = 2` ，因为 `11!= 39916800` 末端有 2 个 0 。

给定 `k`，找出返回能满足 `f(x) = k` 的非负整数 `x` 的数量。

**示例 1：**

```
输入：k = 0
输出：5
解释：0!, 1!, 2!, 3!, 和 4! 均符合 k = 0 的条件。
```

**示例 2：**

```
输入：k = 5
输出：0
解释：没有匹配到这样的 x!，符合 k = 5 的条件。
```

**示例 3:**

```
输入: k = 3
输出: 5
```

 

**提示:**

- $0 <= k <= 10^9$

## 我的解法

想到了部分规律！写不出解法！

## 其它解法

### 二分查找

```c++
class Solution {
public:
    int zeta(long x) {
        int res = 0;
        while (x) {
            res += x / 5;
            x /= 5;
        }
        return res;
    }

    long long help(int k) {
        long long r = 5LL * k;
        long long l = 0;
        while (l <= r) {
            long long mid = (l + r) / 2;
            if (zeta(mid) < k) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return r + 1;
    }

    int preimageSizeFZF(int k) {
        return help(k + 1) - help(k);
    }
};
```

```java
class Solution {
    public int preimageSizeFZF(int k) {
        return (int) (help(k + 1) - help(k));
    }

    public long help(int k) {
        long r = 5L * k;
        long l = 0;
        while (l <= r) {
            long mid = (l + r) / 2;
            if (zeta(mid) < k) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return r + 1;
    }

    public long zeta(long x) {
        long res = 0;
        while (x != 0) {
            res += x / 5;
            x /= 5;
        }
        return res;
    }
}
```

```python
class Solution:
    def preimageSizeFZF(self, k: int) -> int:
        def zeta(n: int) -> int:
            res = 0
            while n:
                n //= 5
                res += n
            return res

        def nx(k: int) -> int:
            return bisect_left(range(5 * k), k, key=zeta)

        return nx(k + 1) - nx(k)
```

#### 思路与算法

首先我们令$ \textit{zeta}(x)$ 为 x! 末尾零的个数。TOP_172. 阶乘后的零 ，有

$$
\textit{zeta}(x) = \sum_{k = 1}^{\infty}\left\lfloor{\frac{x}{5^k}}\right\rfloor
$$
记 $n_{x}$表示 x! 末尾零的个数不小于 x 的最小数，那么题目等价于求解 $n_{k + 1} - n_k$。

由于 $\textit{zeta}(x) $为单调不减函数，因此 $n_{k + 1}$可以通过「二分查找」来求解。

又因为

$$
\textit{zeta}(x) = \sum_{k = 1}^{\infty}\left\lfloor{\frac{x}{5^k}}\right\rfloor \ge \left\lfloor{\frac{x}{5}}\right\rfloor
$$
得

$\textit{zeta}(5x) \ge x$

所以当二分求解$ n_{x}$ 时，我们可以将二分的初始右边界 r 设置为 5x。

#### 复杂度分析

- 时间复杂度：$O(\log^2 k)$，其中 k 为题目给定数字，二分查找 $n_{k + 1}, n_k$ 的时间复杂度为 $O(\log k)$，其中每一步计算 $\textit{zeta}(x)$ 的时间复杂度为 $O(\log k)$。
- 空间复杂度：O(1)，$\textit{zeta}(x)$ 仅为常量空间开销。

