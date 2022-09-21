# 题目

对于某些非负整数 `k` ，如果交换 `s1` 中两个字母的位置恰好 `k` 次，能够使结果字符串等于 `s2` ，则认为字符串 `s1` 和 `s2` 的 **相似度为** `k` **。**

给你两个字母异位词 `s1` 和 `s2` ，返回 `s1` 和 `s2` 的相似度 `k` 的最小值。

**示例 1：**

```
输入：s1 = "ab", s2 = "ba"
输出：1
```

**示例 2：**

```
输入：s1 = "abc", s2 = "bca"
输出：2
```

**提示：**

- `1 <= s1.length <= 20`
- `s2.length == s1.length`
- `s1` 和 `s2` 只包含集合 `{'a', 'b', 'c', 'd', 'e', 'f'}` 中的小写字母
- `s2` 是 `s1` 的一个字母异位词

## 我的解法

dfs，查看题解主题思路后写出！

```py
class Solution:
    def kSimilarity(self, s1: str, s2: str) -> int:
        n = len(s1)
        sl2 = list(s2)

        @cache
        def dfs(s, num):
            if s == s2:
                return num

            sl = list(s)
            ch = None
            point = 0
            for i in range(n):
                if sl[i] != sl2[i]:
                    ch = sl2[i]
                    point = i 
                    break

            ans = float('inf')
            for j in range(point + 1, n):
                if sl[j] == ch:
                    mid = sl[:]
                    mid[point], mid[j] = mid[j], mid[point]
                    mid = "".join(mid)
                    midAns = dfs(mid, num + 1)
                    ans = min(ans, midAns)
            return ans 
        ans = dfs(s1, 0)
        return ans 
```

## 其它解法

### 广度优先搜索

```c++
class Solution {
public:
    int kSimilarity(string s1, string s2) {
        int n = s1.size();
        queue<pair<string, int>> qu;
        unordered_set<string> visit;
        qu.emplace(s1, 0);
        visit.emplace(s1);
        for (int step = 0;; step++) {
            int sz = qu.size();
            for (int i = 0; i < sz; i++) {
                auto [cur, pos] = qu.front();
                qu.pop();
                if (cur == s2) {
                    return step;
                }
                while (pos < n && cur[pos] == s2[pos]) {
                    pos++;
                }
                for (int j = pos + 1; j < n; j++) {
                    if (cur[j] != s2[j] && cur[j] == s2[pos]) { // 剪枝，只在 cur[j] != s2[j] 时去交换
                        swap(cur[pos], cur[j]);
                        if (!visit.count(cur)) {
                            visit.emplace(cur);
                            qu.emplace(cur, pos + 1);
                        }
                        swap(cur[pos], cur[j]);
                    }
                }
            }
        }
    }
};
```

```java
class Solution {
    public int kSimilarity(String s1, String s2) {
        int n = s1.length();
        Queue<Pair<String, Integer>> queue = new ArrayDeque<Pair<String, Integer>>();
        Set<String> visit = new HashSet<String>();
        queue.offer(new Pair<String, Integer>(s1, 0));
        visit.add(s1);
        int step = 0;
        while (!queue.isEmpty()) {
            int sz = queue.size();
            for (int i = 0; i < sz; i++) {
                Pair<String, Integer> pair = queue.poll();
                String cur = pair.getKey();
                int pos = pair.getValue();
                if (cur.equals(s2)) {
                    return step;
                }
                while (pos < n && cur.charAt(pos) == s2.charAt(pos)) {
                    pos++;
                }
                for (int j = pos + 1; j < n; j++) {
                    if (s2.charAt(j) == cur.charAt(j)) {
                        continue;
                    }
                    if (s2.charAt(pos) == cur.charAt(j)) {
                        String next = swap(cur, pos, j);
                        if (!visit.contains(next)) {
                            visit.add(next);
                            queue.offer(new Pair<String, Integer>(next, pos + 1));
                        }
                    }
                }
            }
            step++;
        } 
        return step;
    }

    public String swap(String cur, int i, int j) {
        char[] arr = cur.toCharArray();
        char c = arr[i];
        arr[i] = arr[j];
        arr[j] = c;
        return new String(arr);
    }
}
```

```python
class Solution:
    def kSimilarity(self, s1: str, s2: str) -> int:
        step, n = 0, len(s1)
        q, vis = [(s1, 0)], {s1}
        while True:
            tmp = q
            q = []
            for s, i in tmp:
                if s == s2:
                    return step
                while i < n and s[i] == s2[i]:
                    i += 1
                for j in range(i + 1, n):
                    if s[j] == s2[i] != s2[j]:  # 剪枝，只在 s[j] != s2[j] 时去交换
                        t = list(s)
                        t[i], t[j] = t[j], t[i]
                        t = ''.join(t)
                        if t not in vis:
                            vis.add(t)
                            q.append((t, i + 1))
            step += 1
```

由于题目中给定的字符串的长度范围为 $[1,20]$ 且只包含 6 种不同的字符，因此我们可以枚举所有可能的交换方案，在搜索时进行减枝从而提高搜索效率，最终找到最小的交换次数。

设字符串的长度为 n，如果当前第 i 个字符满足 $s_1[i] \neq s_2[i]$，则从 $s_1[i+1, \cdots]$ 选择一个合适的字符 $s_1[j]$ 进行交换，其中满足 $s_1[j] = s_2[i],j \in [i+1,n-1]$。每次我们进行交换时，可将字符串 $s_1$的前 x 个字符通过交换使得 $s_1[0,\cdots,x - 1] = s_2[0,\cdots,x - 1]$，最终使得 $s_1$的所有字符与 $s_2$相等即可。我们通过以上变换，找到最小的交换次数使得 $s_1$与 $s_2$相等。

在搜索时，我们需要进行减枝，我们设当前的通过交换后的字符串 $s_1'$为一个中间状态，用哈希表记录这些中间状态，当通过交换时发现当前状态已经计算过，则此时我们可以直接跳过该状态。

#### **复杂度分析**

该方法时空复杂度分析较为复杂，暂不讨论。

### 深度优先搜索

```c++
class Solution {
public:
    int minSwap(const string &s1, const string &s2, const int &pos) {
        int tot = 0;
        for (int i = pos; i < s1.size(); i++) {
            tot += s1[i] != s2[i];
        }
        return (tot + 1) / 2;
    }

    int kSimilarity(string s1, string s2) {
        string str1, str2;
        for (int i = 0; i < s1.size(); i++) {
            if (s1[i] != s2[i]) {
                str1.push_back(s1[i]);
                str2.push_back(s2[i]);
            }
        }
        int n = str1.size();
        if (n == 0) {
            return 0;
        }

        int ans = n - 1;
        function<void(int, int)> dfs = [&](int pos, int cost) {
            if (cost > ans) {
                return;
            }
            while (pos < n && str1[pos] == str2[pos]) {
                pos++;
            }
            if (pos == n) {
                ans = min(ans, cost);
                return;
            }
            /* 当前状态的交换次数下限大于等于当前的最小交换次数 */
            if (cost + minSwap(str1, str2, pos) >= ans) {
                return;
            }
            for (int i = pos + 1; i < n; i++) {
                if (str1[i] == str2[pos]) {
                    swap(str1[i], str1[pos]);
                    dfs(pos + 1, cost + 1);
                    swap(str1[i], str1[pos]);
                }
            }
        };
        dfs(0, 0);
        return ans;
    }
};
```

```java
class Solution {
    int ans;

    public int kSimilarity(String s1, String s2) {
        StringBuilder str1 = new StringBuilder();
        StringBuilder str2 = new StringBuilder();
        for (int i = 0; i < s1.length(); i++) {
            if (s1.charAt(i) != s2.charAt(i)) {
                str1.append(s1.charAt(i));
                str2.append(s2.charAt(i));
            }
        }
        if (str1.length() == 0) {
            return 0;
        }
        ans = str1.length() - 1;
        dfs(0, 0, str1.length(), str1.toString(), str2.toString());
        return ans;
    }

    public void dfs(int pos, int cost, int len, String str1, String str2) {
        if (cost > ans) {
            return;
        }
        while (pos < str1.length() && str1.charAt(pos) == str2.charAt(pos)) {
            pos++;
        }
        if (pos == str1.length()) {
            ans = Math.min(ans, cost);
            return;
        }  
        /* 当前状态的交换次数下限大于等于当前的最小交换次数 */      
        if (cost + minSwap(str1, str2, pos) >= ans) {
            return;
        }
        for (int i = pos + 1; i < str1.length(); i++) {
            if (str1.charAt(i) == str2.charAt(pos)) {
                String str1Next = swap(str1, i, pos);
                dfs(pos + 1, cost + 1, len, str1Next, str2);
            }
        }
    }

    public int minSwap(String s1, String s2, int pos) {
        int tot = 0;
        for (int i = pos; i < s1.length(); i++) {
            tot += s1.charAt(i) != s2.charAt(i) ? 1 : 0;
        }
        return (tot + 1) / 2;
    }

    public String swap(String cur, int i, int j) {
        char[] arr = cur.toCharArray();
        char c = arr[i];
        arr[i] = arr[j];
        arr[j] = c;
        return new String(arr);
    }
}
```

```python
class Solution:
    def kSimilarity(self, s1: str, s2: str) -> int:
        s, t = [], []
        for x, y in zip(s1, s2):
            if x != y:
                s.append(x)
                t.append(y)
        n = len(s)
        if n == 0:
            return 0

        ans = n - 1
        def dfs(i: int, cost: int) -> None:
            nonlocal ans
            if cost > ans:
                return
            while i < n and s[i] == t[i]:
                i += 1
            if i == n:
                ans = min(ans, cost)
                return
            diff = sum(s[j] != t[j] for j in range(i, len(s)))
            min_swap = (diff + 1) // 2
            if cost + min_swap >= ans:  # 当前状态的交换次数下限大于等于当前的最小交换次数
                return
            for j in range(i + 1, n):
                if s[j] == t[i]:
                    s[i], s[j] = s[j], s[i]
                    dfs(i + 1, cost + 1)
                    s[i], s[j] = s[j], s[i]
        dfs(0, 0)
        return ans
```

与方法一同样的交换思路，我们也可以采用深度优先搜索来实现，每次遇到不同的字符 $s_1[i] \neq s_2[i]$ 时，则从 $s_1[i+1, \cdots]$ 中选择处于不同位置的字符 $s_1[j] = s_2[i]$，将其与 $s_1[i]$ 进行交换，然后保持当前子状态，搜索下一个位置 i+1，直到所有字符串 $s_1$ 全部与 $s_2$匹配完成；当前子状态搜索完成后，然后恢复字符串，继续搜索下一个与 $s_2[i]$相等的字符，并进行替换即可。在进行深度优先搜索时，由于每个搜索时每个子树的状态都是不同的，所以也可以不用哈希表去重，但是可以用一些特殊的减枝技巧。我们可以得到相似字符串交换次数的上限与下限：

- 对于长度为 n 的两个相似的字符串 $s_1,s_2$，$s_1$最多需要 n-1 次交换变为 $s_2$，因为每进行一次有效交换时，我们可以将 $s_1$ 的中的一个字符调整到与 $s_2$相同，我们只需要将 $s_1$ 的前 n-1 个字符调整到与 $s_2$的前 n-1 个字符相同，则 $s_1$ 的第 n 个字符此时也一定与 $s_2$ 的第 n 个字符相同，因此我们最多需要 n-1 次交换，即可使得 $s_1, s_2$ 相等。
- 对于长度为 n 的两个相似的字符串 $s_1,s_2$，且对于字符串中任意位置的字符均满足 $s_1[i] \neq s_2[i]$。我们可以观察到此时 $s_1$ 最少需要$ \lfloor \dfrac{n+1}{2} \rfloor$ 次交换变为 $s_2$。每进行一次有效交换时，我们最多可以将 $s_1$ 的中的两个字符调整到与 $s_2$ 相同。比如当满足 $s_1[i] = s_2[j],s_1[j] = s_2[i]$，此时我们交换位置 (i,j)，可以将两个字符调整到正确的位置，我们分两种情况进行讨论：
  - 当 n 为偶数时，由于每次交换时最多可以将两个字符同时移动到正确的位置，因此最少需要 $\dfrac{n}{2} $次交换可以使得 $s_1$与$ s_2$相等。
  - 当 n 为奇数数时，每次交换时最多可以将两个字符同时移动到正确的位置，当最终剩下 3 个字符时，此时我们再交换一次时无法交换两个字符到正确的位置。根据前置条件所有位置的字符均满足$ s_1[i] \neq s_2[i]$，假设此时还剩余 3 个字符满足 $s_1[i] \neq s_2[i],s_1[j] \neq s_2[j],s_1[k] \neq s_2[k]$ 时，则此时任意交换一次两个字符使得 $s_1[i] = s_2[i],s_1[j] = s_2[j]$，还剩余一个字符 $s_1[k],s_2[k]$ 不相等，这与两个字符串相似矛盾，因此还需 2 次交换才能使得 $s_1,s_2$ 中剩余的 3 个字符相等。因此当 n 为奇数时，最少需要 $\dfrac{n+1}{2} $次交换可以使得 $s_1$ 与 $s_2$ 相等。

根据以上结论，我们可以进行如下减枝:

- 我们只需要计算两个字符$ s_1,s_2$中同一位置不同的字符的交换次数即可，同一位置相同的字符直接可以跳过。
- 根据之前的结论，假设当前已经通过计算得到的最少交换次数为 $\textit{ans}$，假设当前字符字符串 $s_1$ 已经过 $\textit{cost}$次交换变为了 $s_1{'}$，此时我们计算出字符串 $s_1^{'}$ 变为 $s_2$ 还需要进行交换次数的下限为 $\text{minSwap}(s_1{'})$，则字符串$ s_1$ 经过交换变为中间状态 $s_1{'}$，然后交换变为$ s_2$ 所需的交换次数的下限为 $\textit{cur} = \textit{cost} + \text{minSwap}(s_1^{'})$，如果当前最少交换次数下限满足 $\textit{cur} \ge \textit{ans}$ 时，则表明当前的字符串 $s_1^{'}$ 已经不是更优的搜索状态，可直接提前终止搜索。

#### **复杂度分析**

该方法时空复杂度分析较为复杂，暂不讨论。

### A* 启发式搜索

```c++
class Solution {
public:
    int minSwap(const string &s1, const string &s2, const int &pos) {
        int tot = 0;
        for (int i = pos; i < s1.size(); i++) {
            tot += s1[i] != s2[i];
        }
        return (tot + 1) / 2;
    }

    int kSimilarity(string s1, string s2) {
        typedef tuple<int, int, int, string> State;
        int n = s1.size();
        priority_queue<State, vector<State>, greater<State>> pq;
        unordered_set<string> visit;
        pq.emplace(0, 0, 0, s1);
        visit.emplace(s1);
        while (!pq.empty()) {
            auto [_, cost, pos, cur] = pq.top();
            pq.pop();
            if (cur == s2) {
                return cost;
            }
            while (pos < n && cur[pos] == s2[pos]) {
                pos++;
            }
            for (int j = pos + 1; j < n; j++) {
                if (s2[j] == cur[j]) {
                    continue;
                }
                if (s2[pos] == cur[j]) {
                    swap(cur[pos], cur[j]);
                    if (!visit.count(cur)) {
                        visit.emplace(cur);
                        pq.emplace(cost + 1 + minSwap(s2, cur, pos + 1), cost + 1, pos + 1, cur);
                    }
                    swap(cur[pos], cur[j]);
                }
            }
        } 
        return 0;
    }
};
```

本题我们还可以使用$ \text{A*}$ 启发式搜索，可参考相关 $\text{A*}$ 算法的基础知识，例如「[Wikipedia - A* search algorithm](https://leetcode.cn/link/?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FA*_search_algorithm)」或 「[oi-wiki - A*](https://leetcode.cn/link/?target=https%3A%2F%2Foi-wiki.org%2Fsearch%2Fastar%2F)」，力扣上也可以参考类似题解「[752. 打开转盘锁](https://leetcode.cn/problems/open-the-lock/solution/da-kai-zhuan-pan-suo-by-leetcode-solutio-l0xo/)」。

设估计函数为 f(x) = g(x) + h(x)，其中 g(x) 表示起始状态到达状态 x 的实际交换次数，h(x) 为启发函数，在这里我们设 h(x) 表示状态 x 到达终态可能的最小交换次数，即方法二中提到的当前状态 x 还需要的交换次数的下限 $\text{minSwap}(x)$，h(x) 满足小于等于实际的最小步数。实际上我们观察到该启发函数本质为一种贪心策略，在同样的状态下尽可能的选择一次交换 (i,j) 使得 $s_1$ 中两个位置 (i,j) 的字符与 $s_2$ 相等，这样才能使得启发函数 h(x) 尽可能的小。

#### **复杂度分析**

启发式搜索不讨论时空复杂度。

### 方法四：动态规划

[相似度为 K 的字符串 - 相似度为 K 的字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/k-similar-strings/solution/xiang-si-du-wei-k-de-zi-fu-chuan-by-leet-8z10/)