# 题目

给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每 `2k` 个字符反转前 `k` 个字符。

- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

 

**示例 1：**

```
输入：s = "abcdefg", k = 2
输出："bacdfeg"
```

**示例 2：**

```
输入：s = "abcd", k = 2
输出："bacd"
```

 

**提示：**

- $1 <= s.length <= 10^4$
- `s` 仅由小写英文组成
- $1 <= k <= 10^4$

## 我的解法

过于辣眼睛，代码极其不简便！

## 其他解法

### 模拟

```c++
class Solution {
public:
    string reverseStr(string s, int k) {
        int n = s.length();
        for (int i = 0; i < n; i += 2 * k) {
            reverse(s.begin() + i, s.begin() + min(i + k, n));
        }
        return s;
    }
};
```

```java
class Solution {
    public String reverseStr(String s, int k) {
        int n = s.length();
        char[] arr = s.toCharArray();
        for (int i = 0; i < n; i += 2 * k) {
            reverse(arr, i, Math.min(i + k, n) - 1);
        }
        return new String(arr);
    }

    public void reverse(char[] arr, int left, int right) {
        while (left < right) {
            char temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }
}
```

```python
class Solution:
    def reverseStr(self, s: str, k: int) -> str:
        t = list(s)
        for i in range(0, len(t), 2 * k):
            t[i: i + k] = reversed(t[i: i + k])
        return "".join(t)
```

我们直接按题意进行模拟：反转每个下标从 2k2*k* 的倍数开始的，长度为 k*k* 的子串。若该子串长度不足 k*k*，则反转整个子串。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是字符串 s 的长度。

- 空间复杂度：O(1)或 O(n)，取决于使用的语言中字符串类型的性质。如果字符串是可修改的，那么我们可以直接在原字符串上修改，空间复杂度为 O(1)，否则需要使用 O(n) 的空间将字符串临时转换为可以修改的数据结构（例如数组），空间复杂度为 O(n)。


