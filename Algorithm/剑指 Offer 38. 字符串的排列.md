# 题目

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

**示例:**

```
输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]
```

**限制：**

```
1 <= s 的长度 <= 8
```

## 我的解法

```python
class Solution:
    def permutation(self, s: str) -> List[str]:
        listS = list(s)
        print(listS)
        res = []

        def mode(newS, st):
            l = len(newS)
            if l == 0:
                if st not in res:
                    res.append(st)
                return

            for i in range(l):
                ms = st
                ms += newS[i]
                midS = newS.copy()
                midS.pop(i)
                mode(midS, ms)

        mode(listS, "")
        return res
```

超时，没有进行剪枝！实际上是不会！

## 其他解法

### 回溯法

```c++
class Solution {
public:
    vector<string> permutation(string s) {
        dfs(s, 0);
        return res;
    }
private:
    vector<string> res;
    void dfs(string s, int x) {
        if(x == s.size() - 1) {
            res.push_back(s);                       // 添加排列方案
            return;
        }
        set<int> st;
        for(int i = x; i < s.size(); i++) {
            if(st.find(s[i]) != st.end()) continue; // 重复，因此剪枝
            st.insert(s[i]);
            swap(s[i], s[x]);                       // 交换，将 s[i] 固定在第 x 位
            dfs(s, x + 1);                          // 开启固定第 x + 1 位字符
            swap(s[i], s[x]);                       // 恢复交换
        }
    }
};
```

```java
class Solution {
    List<String> res = new LinkedList<>();
    char[] c;
    public String[] permutation(String s) {
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);
    }
    void dfs(int x) {
        if(x == c.length - 1) {
            res.add(String.valueOf(c));      // 添加排列方案
            return;
        }
        HashSet<Character> set = new HashSet<>();
        for(int i = x; i < c.length; i++) {
            if(set.contains(c[i])) continue; // 重复，因此剪枝
            set.add(c[i]);
            swap(i, x);                      // 交换，将 c[i] 固定在第 x 位
            dfs(x + 1);                      // 开启固定第 x + 1 位字符
            swap(i, x);                      // 恢复交换
        }
    }
    void swap(int a, int b) {
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}
```

```python
class Solution:
    def permutation(self, s: str) -> List[str]:
        c, res = list(s), []
        def dfs(x):
            if x == len(c) - 1:
                res.append(''.join(c))   # 添加排列方案
                return
            dic = set()
            for i in range(x, len(c)):
                if c[i] in dic: continue # 重复，因此剪枝
                dic.add(c[i])
                c[i], c[x] = c[x], c[i]  # 交换，将 c[i] 固定在第 x 位
                dfs(x + 1)               # 开启固定第 x + 1 位字符
                c[i], c[x] = c[x], c[i]  # 恢复交换
        dfs(0)
        return res
```

**解题思路：**
对于一个长度为 nn 的字符串（假设字符互不重复），其排列方案数共有：
$$
n \times (n-1) \times (n-2) … \times 2 \times 1
$$
排列方案的生成：

根据字符串排列的特点，考虑深度优先搜索所有排列方案。即通过字符交换，先固定第 1 位字符（ n 种情况）、再固定第 2 位字符（ n-1 种情况）、... 、最后固定第 n 位字符（ 1 种情况）。

![Picture1.png](https://pic.leetcode-cn.com/1599403497-KXKQcp-Picture1.png)

**重复排列方案与剪枝：**

当字符串存在重复字符时，排列方案中也存在重复的排列方案。为排除重复方案，需在固定某位字符时，保证 “每种字符只在此位固定一次” ，即遇到重复字符时不交换，直接跳过。从 DFS 角度看，此操作称为 “剪枝” 。

![Picture2.png](https://pic.leetcode-cn.com/1599403497-GATdFr-Picture2.png)

递归解析：

1. 终止条件： 当 x = len(c) - 1 时，代表所有位已固定（最后一位只有 1 种情况），则将当前组合 c 转化为字符串并加入 res ，并返回；
2. 递推参数： 当前固定位 x ；
3. 递推工作： 初始化一个 Set ，用于排除重复的字符；将第 x 位字符与 i \in∈ [x, len(c)] 字符分别交换，并进入下层递归；
	1. 剪枝： 若 c[i] 在 Set 中，代表其是重复字符，因此 “剪枝” ；
	2. 将 c[i] 加入 Set ，以便之后遇到重复字符时剪枝；
	3. 固定字符： 将字符 c[i] 和 c[x] 交换，即固定 c[i] 为当前位字符；
	4. 开启下层递归： 调用 dfs(x + 1) ，即开始固定第 x + 1 个字符；
	5. 还原交换： 将字符 c[i] 和 c[x] 交换（还原之前的交换）；

> 下图中 list 对应文中的列表 c 。

![img](https://pic.leetcode-cn.com/1599403497-OCDfsB-Picture3.png)
![img](https://pic.leetcode-cn.com/1599403497-wEILvT-Picture4.png)
![img](https://pic.leetcode-cn.com/1599403497-eBHuvg-Picture5.png)
![img](https://pic.leetcode-cn.com/1599403497-hCtrpl-Picture6.png)
![img](https://pic.leetcode-cn.com/1599403497-BvuqQX-Picture7.png)
![img](https://pic.leetcode-cn.com/1599403497-QPjcfe-Picture8.png)
![img](https://pic.leetcode-cn.com/1599403497-crBxOP-Picture9.png)
![img](https://pic.leetcode-cn.com/1599403497-lwELHl-Picture10.png)
![img](https://pic.leetcode-cn.com/1599403497-yjHsYa-Picture11.png)
![img](https://pic.leetcode-cn.com/1599403497-oYRpaR-Picture12.png)
![img](https://pic.leetcode-cn.com/1599403497-CadkyC-Picture13.png)
![img](https://pic.leetcode-cn.com/1599403497-Sbywbs-Picture14.png)
![img](https://pic.leetcode-cn.com/1599403497-RNgwPK-Picture15.png)
![img](https://pic.leetcode-cn.com/1599403497-nOToNd-Picture16.png)
![img](https://pic.leetcode-cn.com/1599403497-ddKpYy-Picture17.png)
![img](https://pic.leetcode-cn.com/1599403497-xvTQhj-Picture18.png)

#### 复杂度分析：

- 时间复杂度 $O(N!N)$ ： N 为字符串 s 的长度；时间复杂度和字符串排列的方案数成线性关系，方案数为$ N \times (N-1) \times (N-2) … \times 2 \times 1$ ，即复杂度为 $O(N!)$；字符串拼接操作 join() 使用 O(N) ；因此总体时间复杂度为 $O(N!N)$ 。
- 空间复杂度 $O(N^2)$： 全排列的递归深度为 N ，系统累计使用栈空间大小为 O(N) ；递归中辅助 Set 累计存储的字符数量最多为 N + (N-1) + ... + 2 + 1 = (N+1)N/2N+(N−1)+...+2+1=(N+1)N/2 ，即占用 $O(N^2$)的额外空间。

### 回溯

```c++
class Solution {
public:
    vector<string> rec;
    vector<int> vis;

    void backtrack(const string& s, int i, int n, string& perm) {
        if (i == n) {
            rec.push_back(perm);
            return;
        }
        for (int j = 0; j < n; j++) {
            if (vis[j] || (j > 0 && !vis[j - 1] && s[j - 1] == s[j])) {
                continue;
            }
            vis[j] = true;
            perm.push_back(s[j]);
            backtrack(s, i + 1, n, perm);
            perm.pop_back();
            vis[j] = false;
        }
    }

    vector<string> permutation(string s) {
        int n = s.size();
        vis.resize(n);
        sort(s.begin(), s.end());
        string perm;
        backtrack(s, 0, n, perm);
        return rec;
    }
};
```

```java
class Solution {
    List<String> rec;
    boolean[] vis;

    public String[] permutation(String s) {
        int n = s.length();
        rec = new ArrayList<String>();
        vis = new boolean[n];
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        StringBuffer perm = new StringBuffer();
        backtrack(arr, 0, n, perm);
        int size = rec.size();
        String[] recArr = new String[size];
        for (int i = 0; i < size; i++) {
            recArr[i] = rec.get(i);
        }
        return recArr;
    }

    public void backtrack(char[] arr, int i, int n, StringBuffer perm) {
        if (i == n) {
            rec.add(perm.toString());
            return;
        }
        for (int j = 0; j < n; j++) {
            if (vis[j] || (j > 0 && !vis[j - 1] && arr[j - 1] == arr[j])) {
                continue;
            }
            vis[j] = true;
            perm.append(arr[j]);
            backtrack(arr, i + 1, n, perm);
            perm.deleteCharAt(perm.length() - 1);
            vis[j] = false;
        }
    }
}
```

#### 思路及解法

我们将这个问题看作有 n 个排列成一行的空位，我们需要从左往右依次填入题目给定的 nn 个字符，每个字符只能使用一次。首先可以想到穷举的算法，即从左往右每一个空位都依次尝试填入一个字符，看是否能填完这 n 个空位，编程实现时，我们可以用「回溯法」来模拟这个过程。

定义递归函数$ \texttt{backtrack}(i, \textit{perm})$ 表示当前排列为 $\textit{perm}$，下一个待填入的空位是第 i 个空位（下标从 0 开始）。那么该递归函数分为两个情况：

如果 i=n，说明我们已经填完了 n 个空位，找到了一个可行的解，我们将 $\textit{perm}$ 放入答案数组中，递归结束。

如果 i<n，此时需要考虑第 i 个空位填哪个字符。根据题目要求我们肯定不能填已经填过的字符，因此很容易想到的一个处理手段是我们定义一个标记数组 $\textit{vis}$ 来标记已经填过的字符，那么在填第 i 个字符的时候我们遍历题目给定的 n 个字符，如果这个字符没有被标记过，我们就尝试填入，并将其标记，继续尝试填下一个空位，即调用函数 $\texttt{backtrack}(i + 1, \textit{perm})$。回溯时，我们需要要撤销该空位填的字符以及对该字符的标记，并继续向当前空位尝试填入其他没被标记过的字符。

但是该递归函数并没有满足「全排列不重复」的要求，在重复的字符较多的情况下，该递归函数会生成大量重复的排列。对于任意一个空位，如果存在重复的字符，该递归函数会将它们重复填上去并继续尝试导致最后答案的重复。

解决该问题的一种较为直观的思路是，我们首先生成所有的排列，然后进行去重。而另一种思路是我们通过修改递归函数，使得该递归函数只会生成不重复的序列。

具体地，我们只要在递归函数中设定一个规则，保证在填每一个空位的时候重复字符只会被填入一次。具体地，我们首先对原字符串排序，保证相同的字符都相邻，在递归函数中，我们限制每次填入的字符一定是这个字符所在重复字符集合中「从左往右第一个未被填入的字符」，即如下的判断条件：

```c++
if (vis[j] || (j > 0 && !vis[j - 1] && s[j - 1] == s[j])) {
    continue;
}
```

这个限制条件保证了对于重复的字符，我们一定是从左往右依次填入的空位中的。

#### 复杂度分析

- 时间复杂度：$O(n \times n!)$，其中 n 为给定字符串的长度。这些字符的全部排列有 O(n!) 个，每个排列平均需要 O(n) 的时间来生成。

- 空间复杂度：O(n)。我们需要 O(n) 的栈空间进行回溯，注意返回值不计入空间复杂度。


### 下一个排列

```c++
class Solution {
public:
    bool nextPermutation(string& s) {
        int i = s.size() - 2;
        while (i >= 0 && s[i] >= s[i + 1]) {
            i--;
        }
        if (i < 0) {
            return false;
        }
        int j = s.size() - 1;
        while (j >= 0 && s[i] >= s[j]) {
            j--;
        }
        swap(s[i], s[j]);
        reverse(s.begin() + i + 1, s.end());
        return true;
    }

    vector<string> permutation(string s) {
        vector<string> ret;
        sort(s.begin(), s.end());
        do {
            ret.push_back(s);
        } while (nextPermutation(s));
        return ret;
    }
};
```

```java
class Solution {
    public String[] permutation(String s) {
        List<String> ret = new ArrayList<String>();
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        do {
            ret.add(new String(arr));
        } while (nextPermutation(arr));
        int size = ret.size();
        String[] retArr = new String[size];
        for (int i = 0; i < size; i++) {
            retArr[i] = ret.get(i);
        }
        return retArr;
    }

    public boolean nextPermutation(char[] arr) {
        int i = arr.length - 2;
        while (i >= 0 && arr[i] >= arr[i + 1]) {
            i--;
        }
        if (i < 0) {
            return false;
        }
        int j = arr.length - 1;
        while (j >= 0 && arr[i] >= arr[j]) {
            j--;
        }
        swap(arr, i, j);
        reverse(arr, i + 1);
        return true;
    }

    public void swap(char[] arr, int i, int j) {
        char temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public void reverse(char[] arr, int start) {
        int left = start, right = arr.length - 1;
        while (left < right) {
            swap(arr, left, right);
            left++;
            right--;
        }
    }
}
```



#### 思路及解法

我们可以这样思考：当我们已知了当前的一个排列，我们能不能快速得到字典序中下一个更大的排列呢？

答案是肯定的，参见 TOP_31 下一个排列 ，当我们已知了当前的一个排列，我们可以在 O(n)的时间内计算出字典序下一个中更大的排列。这与 $\texttt{C++}$ 中的 $\texttt{next\_permutation}$ 函数功能相同。

具体地，我们首先对给定的字符串中的字符进行排序，即可得到当前字符串的第一个排列，然后我们不断地计算当前字符串的字典序中下一个更大的排列，直到不存在更大的排列为止即可。

这个方案的优秀之处在于，我们得到的所有排列都不可能重复，这样我们就无需进行去重的操作。同时因为无需使用回溯法，没有栈的开销，算法时间复杂度的常数较小。

#### 复杂度分析

- 时间复杂度：$O(n \times n!)$，其中 n 为给定字符串的长度。我们需要 $O(n \log n)$ 的时间得到第一个排列，$\texttt{nextPermutation}$ 函数的时间复杂度为 O(n)，我们至多执行该函数 O(n!) 次，因此总时间复杂度为 $O(n \times n! + n \log n) = O(n \times n!)$。

- 空间复杂度：O(1)。注意返回值不计入空间复杂度。


