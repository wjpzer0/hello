# 题目

给定一个正整数，返回它在 Excel 表中相对应的列名称。

例如，

```
    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
    ...
```

**示例 1:**

```
输入: 1
输出: "A"
```

**示例 2:**

```
输入: 28
输出: "AB"
```

**示例 3:**

```
输入: 701
输出: "ZY"
```

## 我的解法

```python
class Solution:
    def convertToTitle(self, columnNumber: int) -> str:
        res = ""

        while columnNumber:
            newNum = (columnNumber - 1) % 26
            newChar = chr(ord("A") + newNum)
            res = newChar + res
            columnNumber = (columnNumber - 1) // 26

        return res
```

刚开始做的时候没有仔细分析，多次debug后才写出来！

## 其他解法

### 数学

#### 未优化

```c++
class Solution {
public:
    string convertToTitle(int columnNumber) {
        string ans;
        while (columnNumber > 0) {
            int a0 = (columnNumber - 1) % 26 + 1;
            ans += a0 - 1 + 'A';
            columnNumber = (columnNumber - a0) / 26;
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

```java
class Solution {
    public String convertToTitle(int columnNumber) {
        StringBuffer sb = new StringBuffer();
        while (columnNumber > 0) {
            int a0 = (columnNumber - 1) % 26 + 1;
            sb.append((char)(a0 - 1 + 'A'));
            columnNumber = (columnNumber - a0) / 26;
        }
        return sb.reverse().toString();
    }
}
```

```python
class Solution:
    def convertToTitle(self, columnNumber: int) -> str:
        ans = list()
        while columnNumber > 0:
            a0 = (columnNumber - 1) % 26 + 1
            ans.append(chr(a0 - 1 + ord("A")))
            columnNumber = (columnNumber - a0) // 26
        return "".join(ans[::-1])
```

#### 优化后

```c++
class Solution {
public:
    string convertToTitle(int columnNumber) {
        string ans;
        while (columnNumber > 0) {
            --columnNumber;
            ans += columnNumber % 26 + 'A';
            columnNumber /= 26;
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

```java
class Solution {
    public String convertToTitle(int columnNumber) {
        StringBuffer sb = new StringBuffer();
        while (columnNumber != 0) {
            columnNumber--;
            sb.append((char)(columnNumber % 26 + 'A'));
            columnNumber /= 26;
        }
        return sb.reverse().toString();
    }
}
```

```python
class Solution:
    def convertToTitle(self, columnNumber: int) -> str:
        ans = list()
        while columnNumber > 0:
            columnNumber -= 1
            ans.append(chr(columnNumber % 26 + ord("A")))
            columnNumber //= 26
        return "".join(ans[::-1])
```

在考虑如何将列序号转换成列名称之前，先考虑相反的问题，即如何将列名称转换成列序号。TOP_171 Excel表列序号。

引用该题解的结论，如果列名称的长度为 n，每一位对应的序号为$ [a_{n-1}, a_{n-2}, \ldots, a_0]$，其中对于任意 $0 \le i < n$ 都有 $1 \le a_i \le 26$，则列名称对应的列序号为：

$$
\textit{number} = \sum_{i=0}^{n-1} a_i \times 26^i
$$

将列序号转换成列名称，则是在已知 $\textit{number}$ 的情况下，解出 $a_0$到 $a_{n-1}$的值。

分离出 $a_0$项，提出其余项的公因数 26，上式可以改写为：

$$
\textit{number} = a_0 + 26 \times \sum_{i=1}^{n-1} a_i \times 26^{i-1}
$$

将等式两边同时减 1，得：

$$
\textit{number} - 1 = (a_0 - 1) + 26 \times \Big(\sum_{i=1}^{n-1} a_i \times 26^{i-1}\Big)
$$
由于 $0 \le a_0 - 1 \le 25$，由上式可知，$a_0 - 1$是 $\textit{number} - 1$除以 26 的余数。

这样我们就得到了 $a_0$的值。

在得到 $a_0$的值之后，令 $\textit{number}' = \dfrac{\textit{number} - a_0}{26}$，则有：
$$
\textit{number}' = \sum_{i=1}^{n-1} a_i \times 26^{i-1} = a_1 + 26 \times \sum_{i=2}^{n-1} a_i \times 26^{i-2}
$$

于是使用同样的方法，可以得到 $a_1$的值。

上述过程是一个循环的过程，直至 $\textit{number}'=0$时停止。此时我们就得到了 $a_0$到 $a_{n-1}$的值。拼接这些值对应的字母，即得到了答案。

代码实现时，由于我们计算列名称的顺序是从右往左，因此需要将拼接后的结果反转。

对于整数 n，若 n 能被 26 整除，则有：

$$
\dfrac{n}{26} = \Big\lfloor \dfrac{n+r}{26} \Big\rfloor
$$
其中 $0\le r \le 25$。

因此有：

$$
\begin{aligned} \textit{number}' &= \dfrac{\textit{number} - a_0}{26}\\ &= \Big\lfloor \dfrac{(\textit{number}-a_0)+(a_0-1)}{26} \Big\rfloor\\ &= \Big\lfloor \dfrac{\textit{number}-1}{26} \Big\rfloor \end{aligned}
$$

这里我们用到了 $0 \le a_0 - 1 \le 25$ 这一性质。

#### 复杂度分析

- 时间复杂度：$O(\log_{26} \textit{columnNumber})$。时间复杂度即为将 $\textit{columnNumber}$ 转换成 26 进制的位数。

- 空间复杂度：$O(\log_{26} \textit{columnNumber})$。空间复杂度即为将 $\textit{columnNumber}$ 转换成 26 进制的位数。


