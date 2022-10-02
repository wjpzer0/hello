# 题目

在一个由 `'L'` , `'R'` 和 `'X'` 三个字符组成的字符串（例如`"RXXLRXRXL"`）中进行移动操作。一次移动操作指用一个`"LX"`替换一个`"XL"`，或者用一个`"XR"`替换一个`"RX"`。现给定起始字符串`start`和结束字符串`end`，请编写代码，当且仅当存在一系列移动操作使得`start`可以转换成`end`时， 返回`True`。

**示例 :**

```
输入: start = "RXXLRXRXL", end = "XRLXXRRLX"
输出: True
解释:
我们可以通过以下几步将start转换成end:
RXXLRXRXL ->
XRXLRXRXL ->
XRLXRXRXL ->
XRLXXRRXL ->
XRLXXRRLX
```

**提示：**

- `1 <= len(start) = len(end) <= 10000`。
- `start`和`end`中的字符串仅限于`'L'`, `'R'`和`'X'`。

## 我的解法

查看评论区大佬思路后写出！

思路分析： 题目的意思是说 ‘R’只能向右移动，并且只能移向’X’，‘L’只能向左移动，并且只能移向’X’。

- 第一：如果将start、end中的‘X’全部去掉得到的newStart 和 newEnd相等才有可能转换成功。
- 第二：如果start中'R'的左边'X'的个数超过在end中对应位置的'R'的左边'X'的个数，则不能转换成功，因为start中的'R'只能向右移动，右边的'X'只能增加不能减少
- 第三：如果end中'L'的左边'X'的个数超过在start中对应位置的'L'的左边'X'的个数，则不能转换成功，因为start中的'L'只能向左移动，左边的'X'只能减少不能增加

```python
class Solution:
    def canTransform(self, start: str, end: str) -> bool:
        marks = []
        marke = []
        marksX = []
        markeX = []
        sx = ex = 0
        for s, e in zip(start, end):
            if s != "X":
                marks.append(s)
                marksX.append(sx)
            else:
                sx += 1
            if e != "X":
                marke.append(e)
                markeX.append(ex)
            else:
                ex += 1
        if marks != marke:
            return False
        for mark, s, e in zip(marke, marksX, markeX):
            if mark == "L":
                if e > s:
                    return False
            elif mark == "R":
                if s > e:
                    return False
        return True
```

## 其它解法

### 双指针

```
class Solution {
public:
    bool canTransform(string start, string end) {
        int n = start.length();
        int i = 0, j = 0;
        while (i < n && j < n) {
            while (i < n && start[i] == 'X') {
                i++;
            }
            while (j < n && end[j] == 'X') {
                j++;
            }
            if (i < n && j < n) {
                if (start[i] != end[j]) {
                    return false;
                }
                char c = start[i];
                if ((c == 'L' && i < j) || (c == 'R' && i > j)) {
                    return false;
                }
                i++;
                j++;
            }
        }
        while (i < n) {
            if (start[i] != 'X') {
                return false;
            }
            i++;
        }
        while (j < n) {
            if (end[j] != 'X') {
                return false;
            }
            j++;
        }
        return true;
    }
};
```

```java
class Solution {
    public boolean canTransform(String start, String end) {
        int n = start.length();
        int i = 0, j = 0;
        while (i < n && j < n) {
            while (i < n && start.charAt(i) == 'X') {
                i++;
            }
            while (j < n && end.charAt(j) == 'X') {
                j++;
            }
            if (i < n && j < n) {
                if (start.charAt(i) != end.charAt(j)) {
                    return false;
                }
                char c = start.charAt(i);
                if ((c == 'L' && i < j) || (c == 'R' && i > j)) {
                    return false;
                }
                i++;
                j++;
            }
        }
        while (i < n) {
            if (start.charAt(i) != 'X') {
                return false;
            }
            i++;
        }
        while (j < n) {
            if (end.charAt(j) != 'X') {
                return false;
            }
            j++;
        }
        return true;
    }
}
```

```python
class Solution:
    def canTransform(self, start: str, end: str) -> bool:
        n = len(start)
        i = j = 0
        while i < n and j < n:
            while i < n and start[i] == 'X':
                i += 1
            while j < n and end[j] == 'X':
                j += 1
            if i < n and j < n:
                if start[i] != end[j]:
                    return False
                c = start[i]
                if c == 'L' and i < j or c == 'R' and i > j:
                    return False
                i += 1
                j += 1
        while i < n:
            if start[i] != 'X':
                return False
            i += 1
        while j < n:
            if end[j] != 'X':
                return False
            j += 1
        return True
```

每次移动操作将 $\text{“XL"}$ 替换成$ \text{LX"}$，或将 $\text{"RX"}$ 替换成 $\text{"XR"}$，等价于如下操作：

- 如果一个字符 $\text{‘L'}$ 左侧的相邻字符是 $\text{‘X'}$，则将字符$ \text{‘L'}$向左移动一位，将其左侧的 $\text{‘X'}$ 向右移动一位；

- 如果一个字符 $\text{‘R’}$ 右侧的相邻字符是$ \text{‘X’}$，则将字符 $\text{‘R’}$ 向右移动一位，将其右侧的 $\text{‘X’}$ 向左移动一位。


由于每次移动操作只是交换两个相邻字符，不会增加或删除字符，因此如果可以经过一系列移动操作将 $\textit{start}$转换成$ \textit{end}$，则 $\textit{start}$和$ \textit{end}$满足每一种字符的数量分别相同，字符 $\text{‘L’}$ 和 $\text{‘R’}$ 的相对顺序相同，且每个 $\text{‘L’}$ 在 $\textit{end}$中的下标小于等于对应的 $\text{‘L’}$ 在 $\textit{start}$中的下标，以及每个$ \text{‘R’}$ 在 $\textit{end}$中的下标大于等于对应的 $\text{‘R’ }$在 $\textit{start}$中的下标。

因此，可以通过判断 $\textit{start}$和 $\textit{end}$中的所有 $\text{‘L’}$ 和 $\text{‘R’}$ 是否符合替换后的规则，判断是否可以经过一系列移动操作将$ \textit{start}$转换成 $\textit{end}$。

用 n 表示 $\textit{start}$和 $\textit{end}$的长度，用 i 和 j 分别表示 $\textit{start}$和 $\textit{end}$中的下标，从左到右遍历 $\textit{start}$和 $\textit{end}$，跳过所有的 $\text{‘X’}$，当 i 和 j 都小于 n 时，比较非 $\text{‘X’}$ 的字符：

- 如果$ \textit{start}[i] \ne \textit{end}[j]$，则 $\textit{start}$和 $\textit{end}$中的当前字符不匹配，返回 $\text{false}$；
- 如果$ \textit{start}[i] = \textit{end}[j]$，则当前字符是 $\text{‘L’}$ 时应有 $i \ge j$，当前字符是 \text{‘R’} 时应有 $i \le j$，如果当前字符与两个下标的关系不符合该规则，返回 $\text{false}$。


如果 i 和 j 中有一个下标大于等于 n，则有一个字符串已经遍历到末尾，继续遍历另一个字符串中的其余字符，如果其余字符中出现非 $\text{‘X’}$ 的字符，则该字符不能与任意字符匹配，返回 $\text{false}$。

如果 $\textit{start}$和 $\textit{end}$遍历结束之后没有出现不符合移动操作的情况，则可以经过一系列移动操作将 $\textit{start}$转换成 $\textit{end}$，返回 $\text{true}$。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是字符串 $\textit{start}$和 $\textit{end}$的长度。需要遍历两个字符串各一次。

- 空间复杂度：O(1)。只需要使用常量的额外空间。


