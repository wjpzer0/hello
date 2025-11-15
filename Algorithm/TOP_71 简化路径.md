# 题目

给你一个字符串 `path` ，表示指向某一文件或目录的 Unix 风格 **绝对路径** （以 `'/'` 开头），请你将其转化为更加简洁的规范路径。

在 Unix 风格的文件系统中，一个点（`.`）表示当前目录本身；此外，两个点 （`..`） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。任意多个连续的斜杠（即，`'//'`）都被视为单个斜杠 `'/'` 。 对于此问题，任何其他格式的点（例如，`'...'`）均被视为文件/目录名称。

请注意，返回的 **规范路径** 必须遵循下述格式：

- 始终以斜杠 `'/'` 开头。
- 两个目录名之间必须只有一个斜杠 `'/'` 。
- 最后一个目录名（如果存在）**不能** 以 `'/'` 结尾。
- 此外，路径仅包含从根目录到目标文件或目录的路径上的目录（即，不含 `'.'` 或 `'..'`）。

返回简化后得到的 **规范路径** 。

**示例 1：**

```
输入：path = "/home/"
输出："/home"
解释：注意，最后一个目录名后面没有斜杠。 
```

**示例 2：**

```
输入：path = "/../"
输出："/"
解释：从根目录向上一级是不可行的，因为根目录是你可以到达的最高级。
```

**示例 3：**

```
输入：path = "/home//foo/"
输出："/home/foo"
解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
```

**示例 4：**

```
输入：path = "/a/./b/../../c/"
输出："/c"
```

**提示：**

- `1 <= path.length <= 3000`
- `path` 由英文字母，数字，`'.'`，`'/'` 或 `'_'` 组成。
- `path` 是一个有效的 Unix 风格绝对路径。

## 我的解法

思路没有问题，代码有些冗余！

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        static = path.split('/')
        new = []
        for i in static:
            if i != '':
                new.append(i)
        ans = []
        for i in new:
            if i == ".":
                continue
            elif i == "..":
                if len(ans) != 0:
                    ans.pop()
            else:
                ans.append(i)
        return "/" + "/".join(ans)
```

## 其他解法

### 栈

```c++
class Solution {
public:
    string simplifyPath(string path) {
        auto split = [](const string& s, char delim) -> vector<string> {
            vector<string> ans;
            string cur;
            for (char ch: s) {
                if (ch == delim) {
                    ans.push_back(move(cur));
                    cur.clear();
                }
                else {
                    cur += ch;
                }
            }
            ans.push_back(move(cur));
            return ans;
        };

        vector<string> names = split(path, '/');
        vector<string> stack;
        for (string& name: names) {
            if (name == "..") {
                if (!stack.empty()) {
                    stack.pop_back();
                }
            }
            else if (!name.empty() && name != ".") {
                stack.push_back(move(name));
            }
        }
        string ans;
        if (stack.empty()) {
            ans = "/";
        }
        else {
            for (string& name: stack) {
                ans += "/" + move(name);
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public String simplifyPath(String path) {
        String[] names = path.split("/");
        Deque<String> stack = new ArrayDeque<String>();
        for (String name : names) {
            if ("..".equals(name)) {
                if (!stack.isEmpty()) {
                    stack.pollLast();
                }
            } else if (name.length() > 0 && !".".equals(name)) {
                stack.offerLast(name);
            }
        }
        StringBuffer ans = new StringBuffer();
        if (stack.isEmpty()) {
            ans.append('/');
        } else {
            while (!stack.isEmpty()) {
                ans.append('/');
                ans.append(stack.pollFirst());
            }
        }
        return ans.toString();
    }
}
```

```python
class Solution:
    def simplifyPath(self, path: str) -> str:
        names = path.split("/")
        stack = list()
        for name in names:
            if name == "..":
                if stack:
                    stack.pop()
            elif name and name != ".":
                stack.append(name)
        return "/" + "/".join(stack)
```

#### 思路与算法

我们首先将给定的字符串 $\textit{path}$ 根据 $\texttt{/}$ 分割成一个由若干字符串组成的列表，记为 $\textit{names}$。根据题目中规定的「规范路径的下述格式」，$\textit{names}$ 中包含的字符串只能为以下几种：

- 空字符串。例如当出现多个连续的 $\texttt{/}$，就会分割出空字符串；

- 一个点 $\texttt{.}$；

- 两个点 $\texttt{..}$；

- 只包含英文字母、数字或 $\texttt{\_}$ 的目录名。


对于「空字符串」以及「一个点」，我们实际上无需对它们进行处理，因为「空字符串」没有任何含义，而「一个点」表示当前目录本身，我们无需切换目录。

对于「两个点」或者「目录名」，我们则可以用一个栈来维护路径中的每一个目录名。当我们遇到「两个点」时，需要将目录切换到上一级，因此只要栈不为空，我们就弹出栈顶的目录。当我们遇到「目录名」时，就把它放入栈。

这样一来，我们只需要遍历 $\textit{names}$ 中的每个字符串并进行上述操作即可。在所有的操作完成后，我们将从栈底到栈顶的字符串用 $\texttt{/}$ 进行连接，再在最前面加上 $\texttt{/}$ 表示根目录，就可以得到简化后的规范路径。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是字符串 $\textit{path}$ 的长度。

- 空间复杂度：O(n)。我们需要 O(n) 的空间存储 $\textit{names}$ 中的所有字符串。


