# 题目

给定一个表示数据的整数数组 `data` ，返回它是否为有效的 **UTF-8** 编码。

**UTF-8** 中的一个字符可能的长度为 **1 到 4 字节**，遵循以下的规则：

1. 对于 **1 字节** 的字符，字节的第一位设为 0 ，后面 7 位为这个符号的 unicode 码。
2. 对于 **n 字节** 的字符 (n > 1)，第一个字节的前 n 位都设为1，第 n+1 位设为 0 ，后面字节的前两位一律设为 10 。剩下的没有提及的二进制位，全部为这个符号的 unicode 码。

这是 UTF-8 编码的工作方式：

```
   Char. number range  |        UTF-8 octet sequence
      (hexadecimal)    |              (binary)
   --------------------+---------------------------------------------
   0000 0000-0000 007F | 0xxxxxxx
   0000 0080-0000 07FF | 110xxxxx 10xxxxxx
   0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
   0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

**注意：**输入是整数数组。只有每个整数的 **最低 8 个有效位** 用来存储数据。这意味着每个整数只表示 1 字节的数据。

 

**示例 1：**

```
输入：data = [197,130,1]
输出：true
解释：数据表示字节序列:11000101 10000010 00000001。
这是有效的 utf-8 编码，为一个 2 字节字符，跟着一个 1 字节字符。
```

**示例 2：**

```
输入：data = [235,140,4]
输出：false
解释：数据表示 8 位的序列: 11101011 10001100 00000100.
前 3 位都是 1 ，第 4 位为 0 表示它是一个 3 字节字符。
下一个字节是开头为 10 的延续字节，这是正确的。
但第二个延续字节不以 10 开头，所以是不符合规则的。
```

**提示:**

- $1 <= data.length <= 2 * 10^4$
- `0 <= data[i] <= 255`

## 我的解法

查看评论后写出！

```python
class Solution:
    def validUtf8(self, data: List[int]) -> bool:
        n = 0
        for i in range(len(data)):
            if n > 0:
                if data[i] >> 6 != 2:
                    return False
                n -= 1
            elif data[i] >> 7 == 0:
                n = 0
            elif data[i] >> 5 == 0b110:
                n = 1
            elif data[i] >> 4 == 0b1110:
                n = 2
            elif data[i] >> 3 == 0b11110:
                n = 3
            else:
                return False
        return n == 0
```

## 其他解法

### 遍历 + 位运算

```c++
class Solution {
public:
    static const int MASK1 = 1 << 7;
    static const int MASK2 = (1 << 7) + (1 << 6);

    bool isValid(int num) {
        return (num & MASK2) == MASK1;
    }

    int getBytes(int num) {
        if ((num & MASK1) == 0) {
            return 1;
        }
        int n = 0;
        int mask = MASK1;
        while ((num & mask) != 0) {
            n++;
            if (n > 4) {
                return -1;
            }
            mask >>= 1;
        }
        return n >= 2 ? n : -1;
    }

    bool validUtf8(vector<int>& data) {
        int m = data.size();
        int index = 0;
        while (index < m) {
            int num = data[index];
            int n = getBytes(num);
            if (n < 0 || index + n > m) {
                return false;
            }
            for (int i = 1; i < n; i++) {
                if (!isValid(data[index + i])) {
                    return false;
                }
            }
            index += n;
        }
        return true;
    }
};
```

```java
class Solution {
    static final int MASK1 = 1 << 7;
    static final int MASK2 = (1 << 7) + (1 << 6);

    public boolean validUtf8(int[] data) {
        int m = data.length;
        int index = 0;
        while (index < m) {
            int num = data[index];
            int n = getBytes(num);
            if (n < 0 || index + n > m) {
                return false;
            }
            for (int i = 1; i < n; i++) {
                if (!isValid(data[index + i])) {
                    return false;
                }
            }
            index += n;
        }
        return true;
    }

    public int getBytes(int num) {
        if ((num & MASK1) == 0) {
            return 1;
        }
        int n = 0;
        int mask = MASK1;
        while ((num & mask) != 0) {
            n++;
            if (n > 4) {
                return -1;
            }
            mask >>= 1;
        }
        return n >= 2 ? n : -1;
    }

    public boolean isValid(int num) {
        return (num & MASK2) == MASK1;
    }
}
```

```python
class Solution:
    def validUtf8(self, data: List[int]) -> bool:
        MASK1, MASK2 = 1 << 7, (1 << 7) | (1 << 6)

        def getBytes(num: int) -> int:
            if (num & MASK1) == 0:
                return 1
            n, mask = 0, MASK1
            while num & mask:
                n += 1
                if n > 4:
                    return -1
                mask >>= 1
            return n if n >= 2 else -1

        index, m = 0, len(data)
        while index < m:
            n = getBytes(data[index])
            if n < 0 or index + n > m or any((ch & MASK2) != MASK1 for ch in data[index + 1: index + n]):
                return False
            index += n
        return True
```

#### 思路

如果给定的数组 $\textit{data}$ 是有效的 $\text{UTF-8}$ 编码，则可能由一个或多个 $\text{UTF-8}$ 字符组成。第一个 $\text{UTF-8}$ 字符的长度由 $\textit{data}[0]$ 的值决定。对于从 $\textit{data}[\textit{index}]$ 开始的 $\text{UTF-8}$ 字符，可根据 $\textit{data}[\textit{index}]$ 的值得到该字符的长度 n，如果下一个 $\text{UTF-8}$ 字符存在，则下一个 $\text{UTF-8}$ 字符从下标 $\textit{index} + n$ 开始。因此，如果给定的数组 $\textit{data}$ 是有效的 $\text{UTF-8}$ 编码，则其对应的 $\text{UTF-8}$ 字符序列是唯一的，且每个 $\text{UTF-8}$ 字符的开始下标是确定的。

基于上述分析，可以遍历数组 $\textit{data}$ 得到每个字符的开始下标和长度，并分别判断每个字符是否符合 $\text{UTF-8}$ 编码的规则。

每个 $\text{UTF-8}$ 字符由 1 到 4 个字节组成。以下将每个 $\text{UTF-8}$ 字符的第 1 个字节称为头字节，除了第 1 个字节以外的字节统称为其余字节。

用 m 表示 $\textit{data}$ 的长度，用 $\textit{index}$ 表示 $\text{UTF-8}$ 字符的头字节在 $\textit{data}$ 中的下标，初始时 $\textit{index} = 0$。对于每个字符，执行如下操作。

1. 头字节包含了当前字符的字节数信息，根据头字节计算当前字符字节数的方法如下。

   - 如果头字节的最高位是 0，则当前字符由 1 个字节组成，只有头字节，没有其余字节。

   - 如果头字节的最高位是 1，则计算头字节从最高位开始的连续 1 的个数。如果连续 1 的个数为 2 个到 4 个，则连续 1 的个数表示当前字符的字节数；否则头字节不符合 $\text{UTF-8}$ 编码的规则，$\textit{data}$ 不是有效的 $\text{UTF-8}$ 编码。

2. 当头字节符合 $\text{UTF-8}$ 编码的规则时，根据头字节得到当前字符的字节数为 n，则当前字符包括头字节和 n - 1 个其余字节。如果 $\textit{data}$ 在头字节后面的字节数小于 n - 1，即 $\textit{index} + n > m$，则 $\textit{data}$ 不是有效的 $\text{UTF-8}$ 编码。

3. 当 $\textit{data}$ 在头字节后面的字节数大于等于 n - 1 时，头字节后面的 n - 1 个字节为当前字符的其余字节。判断每个其余字节的最高两位是否是 10，如果存在一个其余字节的最高两位不是 10，则 $\textit{data}$ 不是有效的 $\text{UTF-8}$ 编码。

4. 当前字符遍历结束之后，将 $\textit{index}$ 的值加 n，则更新后的 $\textit{index}$ 是下一个字符的头字节在 $\textit{data}$ 中的下标。

5. 重复上述操作，直到 $\textit{index} = n$ 时遍历结束，此时 $\textit{data}$ 是有效的 $\text{UTF-8}$ 编码。


#### 实现

判断 $\textit{data}$ 是否是有效的 $\text{UTF-8}$ 编码时，需要对每个字符的头字节和其余字节分别做判断。判断需要通过位运算实现，为了方便判断，需要设计两个位掩码 $\textit{MASK}_1 = 2^7$，$\textit{MASK}_2 = 2^7 + 2^6$。

对于头字节，首先判断头字节和 $\textit{MASK}_1$的按位与运算结果是否为 0。如果为00 则当前字符由 1 个字节组成。如果不为 0 则创建位掩码 $\textit{mask}$ 并将初始值设为 $\textit{MASK}_1$，每次计算头字节和 $\textit{mask}$ 的按位与运算结果，如果结果不为 0 则将 $\textit{mask}$ 除以 2（可通过右移位运算实现）并重复该过程，直到结果为 0，此时可得到当前字符的字节数。

对于其余字节，判断最高两位是否是 10 的做法为计算其余字节和 $\textit{MASK}_2$的按位与运算结果是否等于 $\textit{MASK}_1$。

#### 复杂度分析

- 时间复杂度：O(m)，其中 m 是数组 $\textit{data}$ 的长度。需要遍历数组 $\textit{data}$ 一次，对于数组中的每个元素的计算时间都是 O(1)。

- 空间复杂度：O(1)。


