# 题目

给定一个字符串 `s`，计算 `s` 的 **不同非空子序列** 的个数。因为结果可能很大，所以返回答案需要对 **`10^9 + 7` 取余** 。

字符串的 **子序列** 是经由原字符串删除一些（也可能不删除）字符但不改变剩余字符相对位置的一个新字符串。

- 例如，`"ace"` 是 `"***a***b***c***d***e***"` 的一个子序列，但 `"aec"` 不是。

**示例 1：**

```
输入：s = "abc"
输出：7
解释：7 个不同的子序列分别是 "a", "b", "c", "ab", "ac", "bc", 以及 "abc"。
```

**示例 2：**

```
输入：s = "aba"
输出：6
解释：6 个不同的子序列分别是 "a", "b", "ab", "ba", "aa" 以及 "aba"。
```

**示例 3：**

```
输入：s = "aaa"
输出：3
解释：3 个不同的子序列分别是 "a", "aa" 以及 "aaa"。
```

**提示：**

- `1 <= s.length <= 2000`
- `s` 仅由小写英文字母组成

## 我的解法

未解出！

### 大佬的解题思路

根据题目描述，要找出一个字符串中所有不同的子序列。那么我们就需要找出这种子序列组合的规律。为了排除其他干扰，我们假设字符串中素有的字符都是不重复的。如下图所示，s=“abcd”，那么我们可以看到如下规律：

遍历第1个字符‘a’：子序列总数 = 1（字符‘a’本身）= 1
遍历第2个字符‘b’：子序列总数 =【字符'a'的子序列总数】+ 1（字符‘b’本身）= 1 + 1 = 2；
遍历第3个字符‘c’：子序列总数 =【字符'a'的子序列总数】+ 【字符'b'的子序列总数】+ 1（字符‘c’本身）= 1 + 2 + 1 = 4；
遍历第4个字符‘d’：子序列总数 =【字符'a'的子序列总数】+【字符'b'的子序列总数】+【字符'c'的子序列总数】+ 1（字符‘d’本身）= 1 + 2 + 4 + 1 = 8；
【总结果】 = 1 + 2 + 4 + 8 = 15

![image.png](https://pic.leetcode-cn.com/1665720158-woONGI-image.png)

但是，题目中并没有限制字符不能重复，所以，我们这时候在考虑如果字符串中出现重复字符，对总结果的影响是怎样的？请见下图，我们以s=“abcb”为例，我们发现，里面有字符‘b’发生了重复，我们发现如下规律：

在第1次遍历到字符‘b’的时候：子序列为“ab”、“b”；
在第2次遍历到字符‘b’的时候：子序列为“ab”、“b”、“abb”、“bb”、“acb”、“abcb”、“bcb”、“cb”；
【结论】我们发现第2次遍历字符'b'的时候，已经包含了第1次遍历字符'b'的子序列了。所以，在统计最终结果的时候，我们需要把“上一次”相同字符子序列总数减去才可以。

![image.png](https://pic.leetcode-cn.com/1665720178-ZBTPiK-image.png)

## 其它解法

### 动态规划

```c++
class Solution {
public:
    int distinctSubseqII(string s) {
        vector<int> last(26, -1);
        
        int n = s.size();
        vector<int> f(n, 1);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < 26; ++j) {
                if (last[j] != -1) {
                    f[i] = (f[i] + f[last[j]]) % mod;
                }
            }
            last[s[i] - 'a'] = i;
        }
        
        int ans = 0;
        for (int i = 0; i < 26; ++i) {
            if (last[i] != -1) {
                ans = (ans + f[last[i]]) % mod;
            }
        }
        return ans;
    }

private:
    static constexpr int mod = 1000000007;
};
```

```java
class Solution {
    public int distinctSubseqII(String s) {
        final int MOD = 1000000007;
        int[] last = new int[26];
        Arrays.fill(last, -1);

        int n = s.length();
        int[] f = new int[n];
        Arrays.fill(f, 1);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < 26; ++j) {
                if (last[j] != -1) {
                    f[i] = (f[i] + f[last[j]]) % MOD;
                }
            }
            last[s.charAt(i) - 'a'] = i;
        }

        int ans = 0;
        for (int i = 0; i < 26; ++i) {
            if (last[i] != -1) {
                ans = (ans + f[last[i]]) % MOD;
            }
        }
        return ans;
    }
}
```

```python
class Solution:
    def distinctSubseqII(self, s: str) -> int:
        mod = 10**9 + 7
        last = [-1] * 26

        n = len(s)
        f = [1] * n
        for i, ch in enumerate(s):
            for j in range(26):
                if last[j] != -1:
                    f[i] = (f[i] + f[last[j]]) % mod
            last[ord(s[i]) - ord("a")] = i
        
        ans = 0
        for i in range(26):
            if last[i] != -1:
                ans = (ans + f[last[i]]) % mod
        return ans
```

#### 思路与算法

我们用 f[i] 表示以 s[i] 为最后一个字符的子序列的数目。

如果子序列中只有 s[i] 这一个字符，那么有一种方案；

如果子序列中至少有两个字符，那么我们可以枚举倒数第二个字符来进行状态转移。容易想到的是：倒数第二个字符可以选择 $s[0], s[1], \cdots, s[i-1]$ 中的某一个，这样就可以得到如下的状态转移方程：

$$
f[i] = f[0] + f[1] + \cdots f[i-1]
$$
然而这样做会产生重复计数。如果 $s[j_0]$ 和 $s[j_1]$ 这两个字符不相同，那么 $f[j_0]$和 $f[j_1]$ 对应的子序列是两个不相交的集合；但如果 $s[j_0]$ 和 $s[j_1]$ 这两个字符相同，那么 $f[j_0]$ 和$ f[j_1]$ 对应的子序列会包含重复的项。最简单的一个重复项就是只含有一个字符的子序列 $s[j_0]$ 或者 $s[j_1]$ 本身。

那么我们该如何防止重复计数呢？可以发现，如果 $j_0<j_1$，那么 $f[j_0]$ 一定是 $f[j_1]$ 的一个真子集。这是因为：

> 每一个以 $s[j_0]$ 为最后一个字符的子序列，都可以把这个字符改成完全相同的 $s[j_1]$，计入到$ f[j_1]$ 中。

因此，对于每一种字符，我们只需要找到其最后一次出现的位置（并且在位置 i 之前），并将对应的 ff 值累加进 f[i] 即可。由于本题中字符串只包含小写字母，我们可以用 $\textit{last}[k]$ 记录第 $k~(0 \leq k < 26)$ 个小写字母最后一次出现的位置。如果它还没有出现过，那么 $\textit{last}[k] = -1$。这样我们就可以写出正确的状态转移方程：

$$
f[i] = \sum_{0\leq k<26,~ \textit{last}[k] \neq -1} f[\textit{last}[k]]
$$
将这两种情况合并在一起，最终的状态转移方程即为：

$$
f[i] = 1 + \sum_{0\leq k<26,~ \textit{last}[k] \neq -1} f[\textit{last}[k]]
$$
在计算完 f[i] 后，我们需要记得更新对应的$ \textit{last}$项。最终的答案即为：

$$
\sum_{0\leq k<26,~ \textit{last}[k] \neq -1} f[\textit{last}[k]]
$$


#### 复杂度分析

- 时间复杂度：$O(n|\Sigma|)$，其中 n 是字符串 s 的长度，$\Sigma$ 是字符集，在本题中 $|\Sigma|=26$。即为动态规划需要的时间。

- 空间复杂度：$O(n + |\Sigma|)$。即为数组 f 和 $\textit{last}$需要的空间。


### 优化的动态规划

```c++
class Solution {
public:
    int distinctSubseqII(string s) {
        vector<int> g(26, 0);
        int n = s.size();
        for (int i = 0; i < n; ++i) {
            int total = 1;
            for (int j = 0; j < 26; ++j) {
                total = (total + g[j]) % mod;
            }
            g[s[i] - 'a'] = total;
        }
        
        int ans = 0;
        for (int i = 0; i < 26; ++i) {
            ans = (ans + g[i]) % mod;
        }
        return ans;
    }

private:
    static constexpr int mod = 1000000007;
};
```

```java
class Solution {
    public int distinctSubseqII(String s) {
        final int MOD = 1000000007;
        int[] g = new int[26];
        int n = s.length();
        for (int i = 0; i < n; ++i) {
            int total = 1;
            for (int j = 0; j < 26; ++j) {
                total = (total + g[j]) % MOD;
            }
            g[s.charAt(i) - 'a'] = total;
        }

        int ans = 0;
        for (int i = 0; i < 26; ++i) {
            ans = (ans + g[i]) % MOD;
        }
        return ans;
    }
}
```

```python
class Solution:
    def distinctSubseqII(self, s: str) -> int:
        mod = 10**9 + 7

        g = [0] * 26
        for i, ch in enumerate(s):
            total = (1 + sum(g)) % mod
            g[ord(s[i]) - ord("a")] = total
        
        return sum(g) % mod
```

#### 思路与算法

观察方法一中的状态转移方程：

$$
f[i] = 1 + \sum_{0\leq k<26,~ \textit{last}[k] \neq -1} f[\textit{last}[k]]
$$
我们可以考虑使用一个长度为 $|\Sigma|=26$ 的数组 g 来进行动态规划，其中 g[k] 就表示上述状态转移方程中的 $f[\textit{last}[k]]$。记 $o_i$表示 s[i] 是第 $o_i$ 个字母，我们可以在遍历到 s[i] 时，更新 $g[o_i]$ 的值为：
$$
g[o_i] = 1 + \sum_{0 \leq k < 26} g[k]
$$
即可。当 $\textit{last}[k] = -1$ 时我们无需进行转移，那么只要将数组 g 的初始值设为 0，在累加时就可以达到相同的效果。

#### 复杂度分析

- 时间复杂度：$O(n|\Sigma|)$，其中 n 是字符串 s 的长度，$\Sigma$ 是字符集，在本题中$ |\Sigma|=26$。即为动态规划需要的时间。

- 空间复杂度：$O(|\Sigma|)$。即为数组 g 和 $\textit{last}$需要的空间。


### 继续优化的动态规划

```c++
class Solution {
public:
    int distinctSubseqII(string s) {
        vector<int> g(26, 0);
        int n = s.size(), total = 0;
        for (int i = 0; i < n; ++i) {
            int oi = s[i] - 'a';
            int prev = g[oi];
            g[oi] = (total + 1) % mod;
            total = ((total + g[oi] - prev) % mod + mod) % mod;
        }
        return total;
    }

private:
    static constexpr int mod = 1000000007;
};
```

```java
class Solution {
    public int distinctSubseqII(String s) {
        final int MOD = 1000000007;
        int[] g = new int[26];
        int n = s.length(), total = 0;
        for (int i = 0; i < n; ++i) {
            int oi = s.charAt(i) - 'a';
            int prev = g[oi];
            g[oi] = (total + 1) % MOD;
            total = ((total + g[oi] - prev) % MOD + MOD) % MOD;
        }
        return total;
    }
}
```

```python
class Solution:
    def distinctSubseqII(self, s: str) -> int:
        mod = 10**9 + 7

        g = [0] * 26
        total = 0
        for i, ch in enumerate(s):
            oi = ord(s[i]) - ord("a")
            g[oi], total = (total + 1) % mod, (total * 2 + 1 - g[oi]) % mod
        
        return total
```

#### 思路与算法

观察方法二中的状态转移方程：

$$
g[o_i] = 1 + \sum_{0 \leq k < 26} g[k]
$$
由于我们的答案是数组 g 的和，而遍历 s[i]后只有 $g[o_i]$ 的值发生了变化。因此我们可以使用一个变量 $\textit{total}$直接维护数组 g 的和，每次将 $g[o_i]$ 的值更新为 $1 + \textit{total}$，再将 $\textit{total}$ 的值增加 $g[o_i]$ 的变化量即可。

#### 复杂度分析

时间复杂度：$O(n + |\Sigma|)$。其中 n 是字符串 s 的长度，$\Sigma$ 是字符集，在本题中 $|\Sigma|=26$。初始化需要的时间为 $O(|\Sigma|)$，动态规划需要的时间的为 O(n)。

空间复杂度：$O(|\Sigma|)$。即为数组 g 需要的空间。

