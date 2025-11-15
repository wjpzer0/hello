# 题目

给定字符串列表 `strs` ，返回其中 **最长的特殊序列** 。如果最长特殊序列不存在，返回 `-1` 。

**特殊序列** 定义如下：该序列为某字符串 **独有的子序列（即不能是其他字符串的子序列）**。

 `s` 的 **子序列**可以通过删去字符串 `s` 中的某些字符实现。

- 例如，`"abc"` 是 `"aebdc"` 的子序列，因为您可以删除`"aebdc"`中的下划线字符来得到 `"abc"` 。`"aebdc"`的子序列还包括`"aebdc"`、 `"aeb"` 和 "" (空字符串)。

**示例 1：**

```
输入: strs = ["aba","cdc","eae"]
输出: 3
```

**示例 2:**

```
输入: strs = ["aaa","aaa","aa"]
输出: -1
```

**提示:**

- `2 <= strs.length <= 50`
- `1 <= strs[i].length <= 10`
- `strs[i]` 只包含小写英文字母

## 我的解法

未解出！没有思路！

## 其他解法

### 枚举每个字符串

#### 思路与算法

```c++
class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        auto is_subseq = [](const string& s, const string& t) -> bool {
            int pt_s = 0, pt_t = 0;
            while (pt_s < s.size() && pt_t < t.size()) {
                if (s[pt_s] == t[pt_t]) {
                    ++pt_s;
                }
                ++pt_t;
            }
            return pt_s == s.size();
        };

        int n = strs.size();
        int ans = -1;
        for (int i = 0; i < n; ++i) {
            bool check = true;
            for (int j = 0; j < n; ++j) {
                if (i != j && is_subseq(strs[i], strs[j])) {
                    check = false;
                    break;
                }
            }
            if (check) {
                ans = max(ans, static_cast<int>(strs[i].size()));
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public int findLUSlength(String[] strs) {
        int n = strs.length;
        int ans = -1;
        for (int i = 0; i < n; ++i) {
            boolean check = true;
            for (int j = 0; j < n; ++j) {
                if (i != j && isSubseq(strs[i], strs[j])) {
                    check = false;
                    break;
                }
            }
            if (check) {
                ans = Math.max(ans, strs[i].length());
            }
        }
        return ans;
    }

    public boolean isSubseq(String s, String t) {
        int ptS = 0, ptT = 0;
        while (ptS < s.length() && ptT < t.length()) {
            if (s.charAt(ptS) == t.charAt(ptT)) {
                ++ptS;
            }
            ++ptT;
        }
        return ptS == s.length();
    }
}
```

```python
class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        def is_subseq(s: str, t: str) -> bool:
            pt_s = pt_t = 0
            while pt_s < len(s) and pt_t < len(t):
                if s[pt_s] == t[pt_t]:
                    pt_s += 1
                pt_t += 1
            return pt_s == len(s)
        
        ans = -1
        for i, s in enumerate(strs):
            check = True
            for j, t in enumerate(strs):
                if i != j and is_subseq(s, t):
                    check = False
                    break
            if check:
                ans = max(ans, len(s))
        
        return ans
```

对于给定的某个字符串$ \textit{str}[i]$，如果它的一个子序列 $\textit{sub}$是「特殊序列」，那么 $\textit{str}[i]$ 本身也是一个「特殊序列」。

> 这是因为如果 $\textit{sub}$如果没有在除了 $\textit{str}[i]$ 之外的字符串中以子序列的形式出现过，那么给 $\textit{sub}$不断地添加字符，它也不会出现。而$ \textit{str}[i] $就是 $\textit{sub}$添加若干个（可以为零个）字符得到的结果。

因此我们只需要使用一个双重循环，外层枚举每一个字符串 $\textit{str}[i] $作为特殊序列，内层枚举每一个字符串 $\textit{str}[j]~(i \neq j)$，判断 $\textit{str}[i]$ 是否不为$ \textit{str}[j] $的子序列即可。

要想判断 $\textit{str}[i] $是否为 $\textit{str}[j]$ 的子序列，我们可以使用贪心 + 双指针的方法：即初始时指针 $\textit{pt}_i$和 $\textit{pt}_j$ 分别指向两个字符串的首字符。如果两个字符相同，那么两个指针都往右移动一个位置，表示匹配成功；否则只往右移动指针 $\textit{pt}_j$，表示匹配失败。如果 $\textit{pt}_i$遍历完了整个字符串，就说明$ \textit{str}[i] $是 $\textit{str}[j]$ 的子序列。

在所有满足要求的 $\textit{str}[i]$ 中，我们选出最长的那个，返回其长度作为答案。如果不存在满足要求的字符串，那么返回 -1。

#### 复杂度分析

- 时间复杂度：$O(n^2 \cdot l)$，其中 n 是数组 $\textit{strs}$的长度，$l $是字符串的平均长度。

- 空间复杂度：O(1)。


