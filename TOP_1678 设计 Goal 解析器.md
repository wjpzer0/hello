# 题目

请你设计一个可以解释字符串 `command` 的 **Goal 解析器** 。`command` 由 `"G"`、`"()"` 和/或 `"(al)"` 按某种顺序组成。Goal 解析器会将 `"G"` 解释为字符串 `"G"`、`"()"` 解释为字符串 `"o"` ，`"(al)"` 解释为字符串 `"al"` 。然后，按原顺序将经解释得到的字符串连接成一个字符串。

给你字符串 `command` ，返回 **Goal 解析器** 对 `command` 的解释结果。

**示例 1：**

```
输入：command = "G()(al)"
输出："Goal"
解释：Goal 解析器解释命令的步骤如下所示：
G -> G
() -> o
(al) -> al
最后连接得到的结果是 "Goal"
```

**示例 2：**

```
输入：command = "G()()()()(al)"
输出："Gooooal"
```

**示例 3：**

```
输入：command = "(al)G(al)()()G"
输出："alGalooG"
```

**提示：**

- `1 <= command.length <= 100`
- `command` 由 `"G"`、`"()"` 和/或 `"(al)"` 按某种顺序组成

## 我的解法

思路清晰，快速解决！

```python
class Solution:
    def interpret(self, command: str) -> str:
        i = 0
        n = len(command)
        ans = ""
        while i < n:
            if command[i] == "G":
                ans += "G"
                i += 1
            elif command[i] == "(":
                if command[i + 1] == ")":
                    ans += "o"
                    i += 2
                else:
                    ans += "al"
                    i += 4 
        return ans
```

## 其它解法

### 直接遍历

```c++
class Solution {
public:
    string interpret(string command) {
        string res;
        for (int i = 0; i < command.size(); i++) {
            if (command[i] == 'G') {
                res += "G";
            } else if (command[i] == '(') {
                if (command[i + 1] == ')') {
                    res += "o";
                } else {
                    res += "al";
                }
            }
        }
        return res;
    }
};
```

```java
class Solution {
    public String interpret(String command) {
        StringBuilder res = new StringBuilder();
        for (int i = 0; i < command.length(); i++) {
            if (command.charAt(i) == 'G') {
                res.append("G");
            } else if (command.charAt(i) == '(') {
                if (command.charAt(i + 1) == ')') {
                    res.append("o");
                } else {
                    res.append("al");
                }
            }
        }
        return res.toString();
    }
}
```

```python
class Solution:
    def interpret(self, command: str) -> str:
        res = []
        for i, c in enumerate(command):
            if c == 'G':
                res.append(c)
            elif c == '(':
                res.append('o' if command[i + 1] == ')' else 'al')
        return ''.join(res)
```

#### 思路与算法

根据题意可以知道字符串 $\textit{command}$一定由三种不同的字符串 $\text{"G"},\text{"()"}, \text{"(al)"}$ 组合而成，其中的转换规则如下：

- $\text{"G"}$ 转换为 $\text{"G"}$；
- $\text{"()"} $转换为$ \text{"o"}$；
- $\text{"(al)"} $转换为 $\text{"al"}$；

由于三种不同的字符串由于模式不同，我们可以按照如下规则进行匹配：

- 如果当前第 i 个字符为 $\text{'G'}$，则表示当前字符串模式为 $\text{"G"}$，转换后的结果为 $\text{"G"}$，我们直接在结果中添加 $\text{"G"}$；

- 如果当前第 i 个字符为 $\text{'('}$，则表示当前字符串模式可能为$ \text{"()"}$ 或 $\text{"(al)"}$；

  - 如果第 i+1 个字符为 $\text{')'}$，则当前字符串模式为 $\text{"()"}$，我们应将其转换为 $\text{"o"}$；
  - 如果第 i+1 个字符为 $\text{'a'}$，则当前字符串模式为 $\text{"(al)"}$，我们应将其转换为$\text{"al"}$；

我们按照以上规则进行转换即可得到转换后的结果。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n表示字符串的长度。我们只需遍历一遍字符串即可。

- 空间复杂度：O(1)。除返回值以外不需要额外的空间。


