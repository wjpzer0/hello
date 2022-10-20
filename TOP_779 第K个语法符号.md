# 题目

我们构建了一个包含 `n` 行( **索引从 1 开始** )的表。首先在第一行我们写上一个 `0`。接下来的每一行，将前一行中的`0`替换为`01`，`1`替换为`10`。

- 例如，对于 `n = 3` ，第 `1` 行是 `0` ，第 `2` 行是 `01` ，第3行是 `0110` 。

给定行数 `n` 和序数 `k`，返回第 `n` 行中第 `k` 个字符。（ `k` **从索引 1 开始**）
**示例 1:**

```
输入: n = 1, k = 1
输出: 0
解释: 第一行：0
```

**示例 2:**

```
输入: n = 2, k = 1
输出: 0
解释: 
第一行: 0 
第二行: 01
```

**示例 3:**

```
输入: n = 2, k = 2
输出: 1
解释:
第一行: 0
第二行: 01
```

**提示:**

- `1 <= n <= 30`
- $1 <= k <= 2^{n - 1}$

## 我的解法

找规律，没有想到优化！

```
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1 and k == 1:
            return 0
        num = 2 ** (n - 2)
        if k > num:
            return 1 ^ self.kthGrammar(n - 1, k - num)
        else:
            return self.kthGrammar(n - 1, k)
```

## 其它解法

### 递归

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        if (n == 1) {
            return 0;
        }
        return (k & 1) ^ 1 ^ kthGrammar(n - 1, (k + 1) / 2);
    }
};
```

```java
class Solution {
    public int kthGrammar(int n, int k) {
        if (n == 1) {
            return 0;
        }
        return (k & 1) ^ 1 ^ kthGrammar(n - 1, (k + 1) / 2);
    }
}
```

```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0
        return (k & 1) ^ 1 ^ self.kthGrammar(n - 1, (k + 1) // 2)
```

#### 思路与算法

首先题目给出一个 n 行的表（索引从 1 开始）。并给出表的构造规则为：第一行仅有一个 0，然后接下来的每一行可以由上一行中 0 替换为 01，1 替换为 10 来生成。

- 比如当 n = 3 时，第 1 行是 0，第 2 行是 01，第 3 行是 0110。

现在要求表第 n 行中第 k 个数字，$1 \le k \le 2 ^ n$。首先我们可以看到第 i 行中会有 $2^{i-1}$个数字，$1 \le i \le n$，且其中第 j 个数字按照构造规则会生第 i + 1 行中的第 $2*j - 1$ 和 $2*j$ 个数字，$1 \le j \le 2^{i-1}$。即对于第 i + 1 行中的第 x 个数字 $\textit{num}_1$，$1 \le x \le 2^i$，会被第 i 行中第 $\lfloor \frac{x + 1}{2} \rfloor$ 个数字 $\textit{num}_2$ 生成。且满足规则：

- 当 $\textit{num}_2 = 0$ 时，$\textit{num}_2$会生成 01：

$$
\textit{num}_1 = \begin{cases} 0, & x \equiv 1 \pmod{2} \\ 1, & x \equiv 0 \pmod{2} \\ \end{cases}
$$

- 当$ num_2 = 1$ 时，$\textit{num}_2$会生成 10：

$$
\textit{num}_1 = \begin{cases} 1, & x \equiv 1 \pmod{2} \\ 0, & x \equiv 0 \pmod{2} \\ \end{cases}
$$

并且进一步总结我们可以得到：$\textit{num}_1 = (x \And 1) \oplus 1 \oplus \textit{num}_2$，其中 $\And$ 为「与」运算符， $\oplus$ 为「异或」运算符。那么我们从第 n 不断往上递归求解，并且当在第一行时只有一个数字，直接返回 0 即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为题目给定表的行数，递归深度为 n。
- 空间复杂度：O(n)，其中 n 为题目给定表的行数，主要为递归的空间开销。

### 找规律 + 递归

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        if (k == 1) {
            return 0;
        }
        if (k > (1 << (n - 2))) {
            return 1 ^ kthGrammar(n - 1, k - (1 << (n - 2)));
        }
        return kthGrammar(n - 1, k);
    }
};
```

```java
class Solution {
    public int kthGrammar(int n, int k) {
        if (k == 1) {
            return 0;
        }
        if (k > (1 << (n - 2))) {
            return 1 ^ kthGrammar(n - 1, k - (1 << (n - 2)));
        }
        return kthGrammar(n - 1, k);
    }
}
```

```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if k == 1:
            return 0
        if k > (1 << (n - 2)):
            return 1 ^ self.kthGrammar(n - 1, k - (1 << (n - 2)))
        return self.kthGrammar(n - 1, k)
```

#### 思路与算法

按照方法一，我们可以尝试写表中的前几行：

- 0
- 01
- 0110
- 01101001
- $\cdots$

我们可以注意到规律：每一行的后半部分正好为前半部分的“翻转”——前半部分是 0 后半部分变为 1，前半部分是 1，后半部分变为 0。且每一行的前半部分和上一行相同。我们可以通过「数学归纳法」来进行证明。

有了这个性质，那么我们再次思考原问题：对于查询某一个行第 k 个数字，如果 k 在后半部分，那么原问题就可以转化为求解该行前半部分的对应位置的“翻转”数字，又因为该行前半部分与上一行相同，所以又转化为上一行对应对应的“翻转”数字。那么按照这样一直递归下去，并在第一行时返回数字 0 即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为题目给定表的行数。

- 空间复杂度：O(n)，其中 n 为题目给定表的行数，主要为递归的空间开销。

### 找规律 + 位运算

```c++
class Solution {
public:
    int kthGrammar(int n, int k) {
        // return __builtin_popcount(k - 1) & 1;
        k--;
        int res = 0;
        while (k > 0) {
            k &= k - 1;
            res ^= 1;
        }
        return res;
    }
};
```

```java
class Solution {
    public int kthGrammar(int n, int k) {
        // return Integer.bitCount(k - 1) & 1;
        k--;
        int res = 0;
        while (k > 0) {
            k &= k - 1;
            res ^= 1;
        }
        return res;
    }
}
```

```python
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        # return (k - 1).bit_count() & 1
        k -= 1
        res = 0
        while k:
            k &= k - 1
            res ^= 1
        return res
```

#### 思路与算法

在「方法二」的基础上，我们来进行优化，本质上我们其实只需要求在过程中的“翻转”总次数，如果“翻转”为偶数次则原问题求解为 0，否则为 1。

首先我们修改行的索引从 0 开始，此时原先第 p 行的索引现在为 p - 1 行，第 i 行有 $2 ^ i$ 位。那么对于某一行 i 中下标为 x 的数字，如果 $x < 2^{i - 1}$ 那么等价于求 i - 1 中下标为 x 的数字，否则 x 的二进制位的从右往左第 i + 1 位为 1，此时需要减去该位（“翻转”一次），然后递归求解即可。所以我们可以看到最后“翻转”的总次数只和初始状态下的下标 x 二进制表示中 1 的个数有关。因此原问题中求“翻转”的总次数就等价于求 k - 1 的二进制表示中 1 的个数，TOP_191. 位1的个数。

#### **复杂度分析**

- 时间复杂度：$O(\log k)$，其中 k 为题目给定查询的下标。
- 空间复杂度：O(1)，仅使用常量变量。

