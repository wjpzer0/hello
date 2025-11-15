# 题目

请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2：**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 3：**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

**提示：**

- 输入必须是长度为 `32` 的 **二进制串** 。

##  我的解法

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        num = 0
        while n:
            n = (n-1) & n
            
            num += 1
        
        return num
```

恩，成功解出！

## 其他解法

### 循环检查二进制位

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ret = 0;
        for (int i = 0; i < 32; i++) {
            if (n & (1 << i)) {
                ret++;
            }
        }
        return ret;
    }
};
```

```java
public class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        for (int i = 0; i < 32; i++) {
            if ((n & (1 << i)) != 0) {
                ret++;
            }
        }
        return ret;
    }
}
```

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        ret = sum(1 for i in range(32) if n & (1 << i)) 
        return ret
```

#### 思路及解法

我们可以直接循环检查给定整数 n 的二进制位的每一位是否为 1。

具体代码中，当检查第 i 位时，我们可以让 n 与 $2^i$进行与运算，当且仅当 n 的第 i 位为 1 时，运算结果不为 0。

#### 复杂度分析

- 时间复杂度：O(k)，其中 k 是 $\texttt{int}$型的二进制位数，k=32。我们需要检查 n 的二进制位的每一位，一共需要检查 32 位。

- 空间复杂度：O(1)，我们只需要常数的空间保存若干变量。


### 位运算优化

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ret = 0;
        while (n) {
            n &= n - 1;
            ret++;
        }
        return ret;
    }
};
```

```java
public class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        while (n != 0) {
            n &= n - 1;
            ret++;
        }
        return ret;
    }
}
```

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        ret = 0
        while n:
            n &= n - 1
            ret += 1
        return ret
```

#### 思路及解法

观察这个运算：$n~\&~(n - 1)$，其预算结果恰为把 n 的二进制位中的最低位的 1 变为 0 之后的结果。

如：$6~\&~(6-1) = 4, 6 = (110)_2, 4 = (100)_2$，运算结果 4 即为把 6 的二进制位中的最低位的 1 变为 0 之后的结果。

这样我们可以利用这个位运算的性质加速我们的检查过程，在实际代码中，我们不断让当前的 n 与 n−1 做与运算，直到 n 变为 0 即可。因为每次运算会使得 n 的最低位的 1 被翻转，因此运算次数就等于 n 的二进制位中 1 的个数。

#### 复杂度分析

- 时间复杂度：$O(\log n)$。循环次数等于 n 的二进制位中 1 的个数，最坏情况下 n 的二进制位全部为 1。我们需要循环 $\log n$ 次。

- 空间复杂度：O(1)，我们只需要常数的空间保存若干变量


