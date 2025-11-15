# 题目

1. 使用下面描述的算法可以扰乱字符串 s 得到字符串 t ：
   如果字符串的长度为 1 ，算法停止
2. 如果字符串的长度 > 1 ，执行下述步骤：
	- 在一个随机下标处将字符串分割成两个非空的子字符串。即，如果已知字符串 s ，则可以将其分成两个子字符串 x 和 y ，且满足 s = x + y 。
	- 随机 决定是要「交换两个子字符串」还是要「保持这两个子字符串的顺序不变」。即，在执行这一步骤之后，s 可能是 s = x + y 或者 s = y + x 。
	- 在 x 和 y 这两个子字符串上继续从步骤 1 开始递归执行此算法。

给你两个 长度相等 的字符串 s1 和 s2，判断 s2 是否是 s1 的扰乱字符串。如果是，返回 true ；否则，返回 false 。

示例 1：

```
输入：s1 = "great", s2 = "rgeat"
输出：true
解释：s1 上可能发生的一种情形是：
"great" --> "gr/eat" // 在一个随机下标处分割得到两个子字符串
"gr/eat" --> "gr/eat" // 随机决定：「保持这两个子字符串的顺序不变」
"gr/eat" --> "g/r / e/at" // 在子字符串上递归执行此算法。两个子字符串分别在随机下标处进行一轮分割
"g/r / e/at" --> "r/g / e/at" // 随机决定：第一组「交换两个子字符串」，第二组「保持这两个子字符串的顺序不变」
"r/g / e/at" --> "r/g / e/ a/t" // 继续递归执行此算法，将 "at" 分割得到 "a/t"
"r/g / e/ a/t" --> "r/g / e/ a/t" // 随机决定：「保持这两个子字符串的顺序不变」
算法终止，结果字符串和 s2 相同，都是 "rgeat"
这是一种能够扰乱 s1 得到 s2 的情形，可以认为 s2 是 s1 的扰乱字符串，返回 true
```


示例 2：

```
输入：s1 = "abcde", s2 = "caebd"
输出：false
```


示例 3：

```
输入：s1 = "a", s2 = "a"
输出：true
```


提示：

- s1.length == s2.length
- 1 <= s1.length <= 30
- s1 和 s2 由小写英文字母组成

## 我的解法

想到用动态规划，但是无法理清楚改如何用，未解出！

## 其他解法

### 递归

```python
class Solution:
    @functools.lru_cache(None)
    def isScramble(self, s1: str, s2: str) -> bool:
        N = len(s1)
        if N == 0: return True
        if N == 1: return s1 == s2
        if sorted(s1) != sorted(s2):
            return False
        for i in range(1, N):
            if self.isScramble(s1[:i], s2[:i]) and self.isScramble(s1[i:], s2[i:]):
                return True
            elif self.isScramble(s1[:i], s2[-i:]) and self.isScramble(s1[i:], s2[:-i]):
                return True
        return False
```

**题意**：「扰乱字符串」是指将 `s1` 中的某些字符经过一系列翻转，能否得到 `s2`.

这个题相当于让我们来判断两棵二叉树是否能够通过翻转某些子树而互相得到。这个题不过是把树变成了字符串而已：

![https://pic.leetcode-cn.com/1618538195-WVSNiW-tree_ex.png](https://pic.leetcode-cn.com/1618538195-WVSNiW-tree_ex.png)

**思路**：从一个位置将两个字符串分别划分成两个子串，然后递归判断两个字符串的两个子串是否互相为「扰乱字符串」。

因为不知道在哪个位置分割字符串，所以直接遍历每个位置进行分割。在判断是否两个子串能否通过翻转变成相等的时候，需要保证传给函数的子串长度是相同的。

综上，因此分两种情况讨论：

- s1[0:i]和 s2[0:i]作为左子树，s1[i:N]和 s2[i:N]作为右子树
- s1[0:i]和 s2[N - i:N]作为左子树，s1[i:N]和 s2[0:N-i]作为右子树

其中左子树的两个字符串的长度都是 i, 右子树的两个字符串的长度都是 N - i。如果上面两种情况有一种能够成立，则 s1 和 s2 是「扰乱字符串」。

**递归终止条件**：当长度是 0、长度是 1 时的两个字符串是否相等进行判断。如果两个字符串本身包含的字符就不等，那么一定不是「扰乱字符串」，所以我们对两个字符串排序后是否相等也做判断。

#### 记忆化递归

本题如果直接提交上面的递归解法，会超时，因为在不同的递归输入时，存在对相同子串的重复计算。避免重复计算的方式是使用「记忆化递归」。这个思路不难，就是把已经计算过的结果保存到缓存中，当此后再有同样的递归输入的时候，直接从缓存里面查，从而避免了重复计算。

在 Python 中，有一个实现记忆化递归的神器，就是 functool模块的 lru_cache装饰器，它可以把函数的输入和输出结果缓存住，在后续调用中如果遇到了相同的输入，直接从缓存里面读。顾名思义，它使用的是 LRU （最近最少使用）的缓存淘汰策略。

```
@functools.lru_cache(maxsize=None, typed=False)
```

- maxsize 为最多缓存次数，如果为 None，则无限制；
- typed=True 时，表示不同参数类型的调用将分别缓存。

该装饰器使用方法很简单，看下面代码的第二行。

这一行代码就能化腐朽为神奇，顺利 AC。

#### 复杂度分析

- 时间复杂度：O(N!)
- 空间复杂度：O(N!)

## 动态规划

```python
class Solution:
    def isScramble(self, s1: str, s2: str) -> bool:
        @cache
        def dfs(i1: int, i2: int, length: int) -> bool:
            """
            第一个字符串从 i1 开始，第二个字符串从 i2 开始，子串的长度为 length，是否和谐
            """

            # 判断两个子串是否相等
            if s1[i1:i1+length] == s2[i2:i2+length]:
                return True
            
            # 判断是否存在字符 c 在两个子串中出现的次数不同
            if Counter(s1[i1:i1+length]) != Counter(s2[i2:i2+length]):
                return False
            
            # 枚举分割位置
            for i in range(1, length):
                # 不交换的情况
                if dfs(i1, i2, i) and dfs(i1 + i, i2 + i, length - i):
                    return True
                # 交换的情况
                if dfs(i1, i2 + length - i, i) and dfs(i1 + i, i2, length - i):
                    return True
        
            return False

        ans = dfs(0, 0, len(s1))
        dfs.cache_clear()
        return ans
```

```c++
class Solution {
private:
    // 记忆化搜索存储状态的数组
    // -1 表示 false，1 表示 true，0 表示未计算
    int memo[30][30][31];
    string s1, s2;

public:
    bool checkIfSimilar(int i1, int i2, int length) {
        unordered_map<int, int> freq;
        for (int i = i1; i < i1 + length; ++i) {
            ++freq[s1[i]];
        }
        for (int i = i2; i < i2 + length; ++i) {
            --freq[s2[i]];
        }
        if (any_of(freq.begin(), freq.end(), [](const auto& entry) {return entry.second != 0;})) {
            return false;
        }
        return true;
    }

    // 第一个字符串从 i1 开始，第二个字符串从 i2 开始，子串的长度为 length，是否和谐
    bool dfs(int i1, int i2, int length) {
        if (memo[i1][i2][length]) {
            return memo[i1][i2][length] == 1;
        }

        // 判断两个子串是否相等
        if (s1.substr(i1, length) == s2.substr(i2, length)) {
            memo[i1][i2][length] = 1;
            return true;
        }

        // 判断是否存在字符 c 在两个子串中出现的次数不同
        if (!checkIfSimilar(i1, i2, length)) {
            memo[i1][i2][length] = -1;
            return false;
        }
        
        // 枚举分割位置
        for (int i = 1; i < length; ++i) {
            // 不交换的情况
            if (dfs(i1, i2, i) && dfs(i1 + i, i2 + i, length - i)) {
                memo[i1][i2][length] = 1;
                return true;
            }
            // 交换的情况
            if (dfs(i1, i2 + length - i, i) && dfs(i1 + i, i2, length - i)) {
                memo[i1][i2][length] = 1;
                return true;
            }
        }

        memo[i1][i2][length] = -1;
        return false;
    }

    bool isScramble(string s1, string s2) {
        memset(memo, 0, sizeof(memo));
        this->s1 = s1;
        this->s2 = s2;
        return dfs(0, 0, s1.size());
    }
};
```

```java
class Solution {
    // 记忆化搜索存储状态的数组
    // -1 表示 false，1 表示 true，0 表示未计算
    int[][][] memo;
    String s1, s2;

    public boolean isScramble(String s1, String s2) {
        int length = s1.length();
        this.memo = new int[length][length][length + 1];
        this.s1 = s1;
        this.s2 = s2;
        return dfs(0, 0, length);
    }

    // 第一个字符串从 i1 开始，第二个字符串从 i2 开始，子串的长度为 length，是否和谐
    public boolean dfs(int i1, int i2, int length) {
        if (memo[i1][i2][length] != 0) {
            return memo[i1][i2][length] == 1;
        }

        // 判断两个子串是否相等
        if (s1.substring(i1, i1 + length).equals(s2.substring(i2, i2 + length))) {
            memo[i1][i2][length] = 1;
            return true;
        }

        // 判断是否存在字符 c 在两个子串中出现的次数不同
        if (!checkIfSimilar(i1, i2, length)) {
            memo[i1][i2][length] = -1;
            return false;
        }
        
        // 枚举分割位置
        for (int i = 1; i < length; ++i) {
            // 不交换的情况
            if (dfs(i1, i2, i) && dfs(i1 + i, i2 + i, length - i)) {
                memo[i1][i2][length] = 1;
                return true;
            }
            // 交换的情况
            if (dfs(i1, i2 + length - i, i) && dfs(i1 + i, i2, length - i)) {
                memo[i1][i2][length] = 1;
                return true;
            }
        }

        memo[i1][i2][length] = -1;
        return false;
    }

    public boolean checkIfSimilar(int i1, int i2, int length) {
        Map<Character, Integer> freq = new HashMap<Character, Integer>();
        for (int i = i1; i < i1 + length; ++i) {
            char c = s1.charAt(i);
            freq.put(c, freq.getOrDefault(c, 0) + 1);
        }
        for (int i = i2; i < i2 + length; ++i) {
            char c = s2.charAt(i);
            freq.put(c, freq.getOrDefault(c, 0) - 1);
        }
        for (Map.Entry<Character, Integer> entry : freq.entrySet()) {
            int value = entry.getValue();
            if (value != 0) {
                return false;
            }
        }
        return true;
    }
}
```

### 思路与算法

显然「扰乱字符串」的关系是具有对称性的，即如果 $$s_1$$是 $$s_2$$的扰乱字符串，那么 $$s_2$$也是 $$s_1$$的扰乱字符串。为了叙述方便，我们称这种情况下，$$s_1$$和 $s_2$是「和谐」的。

那么如何判断 s_1和 $s_2$是否「和谐」呢？我们首先可以想到几个简单的判断方法：

- 如果 $s_1$ = $s_2$，那么它们是「和谐」的；
- 如果 s_1和 $s_2$的长度不同，那么它们一定不是「和谐」的；
- 如果 s_1中某个字符 c 出现了 $x_1$次，而 c 在 $s_2$中出现了 $x_2$次，且 $x_1 \neq x_2$，那么它们一定不是「和谐」的。这是因为任意操作都不会改变一个字符串中的字符种类以及数量。

那么对于剩下的情况，我们该如何判断呢？我们可以从 $s_1$的分割方法入手。假设 $s_1$作为根节点时被分割成了 l($s_1$)以及 r($s_1$)两个子串，那么：

- 如果 l($s_1$)和 r($s_1$)没有被交换，那么 $s_2$需要存在一种分割方法 $s_2$ = l($s_2$) + r($s_2$)，使得 l($s_1$)和 l($s_2$)是「和谐」的，并且 r($s_1$)和 r($s_2$) 也是「和谐」的；
- 如果 l($s_1$)和 r($s_1$)被交换了，那么 $s_2$需要存在一种分割方法 $s_2$ = l($s_2$) + r($s_2$)，使得 l($s_1$) 和 r($s_2$)是「和谐」的，并且 r($s_1$) 和 l($s_2$)也是「和谐」的。

![fig1](https://assets.leetcode-cn.com/solution-static/87/1.png)

这样一来，我们就把原本需要解决的问题划分成了两个本质相同，但规模更小的子问题，因此可以考虑使用动态规划解决。

设 f($s_1$, $s_2$)表示 $s_1$和 s_2是否「和谐」，那么我们可以写出状态转移方程：
$$
f(s_1, s_2) = \begin{cases} \text{True}, & \quad s_1=s_2 \\ \text{False}, & \quad 存在某个字符~c，它在~s_1~和~s_2~中的出现次数不同 \\ \end{cases}
$$
因为题目保证给定的原始字符串的长度相同，因此我们只需要判断上面的两种情况。如果 $s_1$和 $s_2$不符合这两种情况，那么我们需要枚举分割点。

设 $s_1$和 $s_2$的长度为 n，我们用 $s_1(x, y)$表示从 $s_1$从第 x 个字符（从 0 开始编号）开始，长度为 y 的子串。由于分割出的两个字符串不能为空串，那么其中一个字符串就是 $s_1(0, i)$，另一个字符串是 $s_1(i, n-i)$。

对于 l($s_1$) 和 r($s_1$)没有被交换的情况，$s_2$同样需要被分为 $s_2(0, i)$以及 $s_2(i, n-i)$，否则长度不同的字符串是不可能「和谐」的。因此我们可以写出状态转移方程：

$$
f(s_1, s_2) = \bigvee_{i=1}^{n-1} \big( f(s_1(0, i), s_2(0, i)) \wedge f(s_1(i, n-i), s_2(i, n-i)) \big)
$$



其中 ∧ 表示与运算，即 $s_1$和 $s_2$分割出的两对字符串都要是「和谐」的；∨ 表示或运算，即只要有一种满足要求的分割方法，$s_1$和 $s_2$就是和谐的。

对于 l($s_1$)和 r($s_1$)被交换的情况，$s_2$需要被分为 $s_2(0, n-i)$以及 $s_2(n-i, i)$，这样对应的长度才会相同。因此我们可以写出状态转移方程：

$$
f(s_1, s_2) = \bigvee_{i=1}^{n-1} \big( f(s_1(0, i), s_2(n-i, i)) \wedge f(s_1(i, n-i), s_2(0, n-i)) \big)
$$
我们将上面两种状态转移方程用 ∨ 或运算拼在一起，即可得到最终的状态转移方程。

#### 细节

细节部分比较长，希望读者仔细阅读，否则写出来的代码可能会较为复杂，或者使用较多不必要的空间。

在进行状态转移时，我们需要先计算出较短的字符串对应的 f 值，再去转移计算出较长的字符串对应的 ff 值，这是因为我们需要保证在计算 $f(s_1, s_2)$时，所有它们的子串对应的状态都需要被计算过。因此，如果我们使用常规的动态规划方法编写代码，可能会受到计算顺序的困扰，使得代码冗长。

而我们可以考虑使用「记忆化搜索」自顶向下地进行动态规划，这样我们只需要用题目中给定的两个原始字符串开始，递归地计算所有的 ff 值，而无需考虑计算顺序。

由于我们使用记忆化搜索，因此我们需要把 $s_1$和 $s_2$作为参数传入记忆化搜索使用的递归函数。这样一来，在递归传递参数的过程中，会使用到大量字符串的切片、拷贝等操作，使得时空复杂度不那么优。本题中，由于给定原始字符串的长度不超过 30，因此不会产生太大的影响，但我们还是要尽可能对代码进行优化。

一种通用的优化方法是，我们将状态变更为$ f(i_1, i_2, \textit{length})$，表示第一个字符串是原始字符串从第 $i_1$个字符开始，长度为 length 的子串，第二个字符串是原始字符串从第 $i_2$个字符开始，长度为 length 的子串。可以发现，我们只是改变了表达 $s_1$和 $s_2$的方式，但此时我们只需要在递归时传递三个整数类型的变量，省去了字符串的操作；

#### 复杂度分析

- 时间复杂度：$O(n^4)$，其中 n 是给定的原始字符串的长度。动态规划中的状态 $f(i_1, i_2, \textit{length})$有 3 个维度，对于每一个状态，我们需要 O(n) 枚举分割位置，因此总时间复杂度为 $O(n^4)$。

- 空间复杂度：$O(n^3$)，即为存储所有动态规划状态需要的空间。


