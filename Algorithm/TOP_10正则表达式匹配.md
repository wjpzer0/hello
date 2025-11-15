#### 题目

给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。
'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

示例 1：
输入：s = "aa" p = "a"
输出：false
解释："a" 无法匹配 "aa" 整个字符串。

示例 2:
输入：s = "aa" p = "a*"
输出：true
解释：因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。

示例 3：
输入：s = "ab" p = ".*"
输出：true
解释：".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。

示例 4：
输入：s = "aab" p = "c*a*b"
输出：true
解释：因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。

示例 5：
输入：s = "mississippi" p = "mis*is*p*."
输出：false

提示：
0 <= s.length <= 20
0 <= p.length <= 30
s 可能为空，且只包含从 a-z 的小写字母。
p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。
保证每次出现字符 * 时，前面都匹配到有效的字符

#### 我的解法

未解出

#### 其他解法

###### 动态规划

```java
class Solution {
    public boolean isMatch(String s, String p) {

        boolean table[][] = new boolean[s.length() + 1][p.length() + 1];

        table[0][0] = true;

        for (int col=1; col<table[0].length; col++) {
            char ch = p.charAt(col-1);
            if (col > 1) {
                if (ch == '*') {
                    table[0][col] = table[0][col-2]; 
                } else {
                    table[0][col] = false;
                }
            } else {
                if (ch == '*') {
                    table[0][col] = true;
                }
            }
        }

        for (int row=1; row<table.length; row++) {
            char ch1 = s.charAt(row-1);
            for (int col=1; col<table[row].length; col++) {
                char ch2 = p.charAt(col-1);
                if (ch1==ch2 || ch2 == '.') {
                    table[row][col] = table[row-1][col-1];
                } else if (ch2 == '*') {
                    if(col > 1) {
                        if (table[row][col-2]) {
                            table[row][col] = true; // * 前面的字符出现0次
                        } else {
                            char prev = p.charAt(col-2);
                            if (prev== ch1 || prev == '.') {
                                table[row][col] = table[row - 1][col]; // * 前面的字符出现多次
                            }
                        }

                    }
                }
            }
        }
        boolean lastRow[] = table[table.length-1];
        return lastRow[lastRow.length-1];
    }
}
```

![TOP_10正则表达式匹配](D:\study\course\数据结构和算法\算法练习\TOP_10正则表达式匹配.png)

```java
class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();

        boolean[][] f = new boolean[m + 1][n + 1];
        f[0][0] = true;
        for (int i = 0; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (p.charAt(j - 1) == '*') {
                    f[i][j] = f[i][j - 2];
                    if (matches(s, p, i, j - 1)) {
                        f[i][j] = f[i][j] || f[i - 1][j];
                    }
                }
                else {
                    if (matches(s, p, i, j)) {
                        f[i][j] = f[i - 1][j - 1];
                    }
                }
            }
        }
        return f[m][n];
    }

    public boolean matches(String s, String p, int i, int j) {
        if (i == 0) {
            return false;
        }
        if (p.charAt(j - 1) == '.') {
            return true;
        }
        return s.charAt(i - 1) == p.charAt(j - 1);
    }
}
```

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)

        def matches(i: int, j: int) -> bool:
            if i == 0:
                return False
            if p[j - 1] == '.':
                return True
            return s[i - 1] == p[j - 1]

        f = [[False] * (n + 1) for _ in range(m + 1)]
        f[0][0] = True
        for i in range(m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == '*':
                    f[i][j] |= f[i][j - 2]
                    if matches(i, j - 1):
                        f[i][j] |= f[i - 1][j]
                else:
                    if matches(i, j):
                        f[i][j] |= f[i - 1][j - 1]
        return f[m][n]
```

状态
首先状态 dp 一定能自己想出来。
dp[i]\[j]表示 s 的前 i 个是否能被 p 的前 j 个匹配

转移方程
怎么想转移方程？首先想的时候从已经求出了 dp[i-1]\[j-1] 入手，再加上已知 s[i]、p[j]，要想的问题就是怎么去求 dp[i]\[j]。
已知 dp[i-1]\[j-1] 意思就是前面子串都匹配上了，不知道新的一位的情况。
那就分情况考虑，所以对于新的一位 p[j] s[i] 的值不同，要分情况讨论：
	考虑最简单的 p[j] == s[i] : dp[i]\[j] = dp[i-1]\[j-1]然后从 p[j] 可能的情况来考虑，让 p[j]=各种能等于的东西。
	p[j] == "." : dp[i]\[j] = dp[i-1]\[j-1]
	p[j] ==" * ":

第一个难想出来的点：怎么区分 * 的两种讨论情况
首先给了 *，明白 * 的含义是 匹配零个或多个前面的那一个元素，所以要考虑他前面的元素 p[j-1]。* 跟着他前一个字符走，前一个能匹配上 s[i]，* 才能有用，前一个都不能匹配上 s[i]，* 也无能为力，只能让前一个字符消失，也就是匹配 00 次前一个字符。
所以按照 p[j-1] 和 s[i] 是否相等，我们分为两种情况：
	3.1 p[j-1] != s[i] : dp[i]\[j] = dp[i]\[j-2]
		这就是刚才说的那种前一个字符匹配不上的情况。
		比如(ab, abc * )。遇到 * 往前看两个，发现前面 s[i] 的 ab 对 p[j-2] 的 ab 能匹配，虽然后面是 c*，但是可以看做匹配 00 次 c，相当于直接去掉 c *，所以也是 True。注意 (ab, abc**) 是 False。
	3.2 p[j-1] == s[i] or p[j-1] == "."：
		前面那个字符，能匹配 s[i]，或者 * 前面那个字符是万能的 .
		因为 . * 就相当于 . .，那就只要看前面可不可以匹配就行。
		比如 (##b , ###b *)，或者 ( ##b , ### . * ) 只看 ### 后面一定是能够匹配上的。
		所以要看 b 和 b * 前面那部分 ## 的地方匹不匹配。

第二个难想出来的点：怎么判断前面是否匹配
dp[i]\[j] = dp[i-1\][j] // 多个字符匹配的情况	
or dp[i]\[j] = dp[i]\[j-1] // 单个字符匹配的情况
or dp[i]\[j] = dp[i]\[j-2] // 没有匹配的情况	
看 ### 匹不匹配，不是直接只看 ### 匹不匹配，要综合后面的 b b* 来分析
这三种情况是 oror 的关系，满足任意一种都可以匹配上，同时是最难以理解的地方：
dp[i-1][j] 就是看 s 里 b 多不多， ### 和 ###b * 是否匹配，一旦匹配，s 后面再添个 b 也不影响，因为有 * 在，也就是 ###b 和 ###b *也会匹配。
dp[i][j-1] 就是去掉 * 的那部分，###b 和 ###b 是否匹配，比如 qqb qqb
dp[i][j-2] 就是 去掉多余的 b *，p 本身之前的能否匹配，###b 和 ### 是否匹配，比如 qqb qqbb* 之前的 qqb qqb 就可以匹配，那多了的 b * 也无所谓，因为 b * 可以是匹配 00 次 b，相当于 b * 可以直接去掉了。
三种满足一种就能匹配上。
为什么没有 dp[i-1]\[j-2] 的情况？ 就是 ### 和 ### 是否匹配？因为这种情况已经是 dp[i][j-1] 的子问题。也就是 s[i]==p[j-1]，则 dp[i-1]\[j-2]=dp[i]\[j-1]。

最后来个归纳：
如果 p.charAt(j) == s.charAt(i) : dp[i][j] = dp[i-1]\[j-1]；
如果 p.charAt(j) == '.' : dp[i][j] = dp[i-1]\[j-1]；
如果 p.charAt(j) == '*'：
如果 p.charAt(j-1) != s.charAt(i) : dp[i][j] = dp[i][j-2] //in this case, a* only counts as empty
如果 p.charAt(i-1) == s.charAt(i) or p.charAt(i-1) == '.'：
dp[i]\[j] = dp[i-1]\[j] //in this case, a* counts as multiple a
or dp[i]\[j] = dp[i]\[j-1] // in this case, a* counts as single a
or dp[i]\[j] = dp[i]\[j-2] // in this case, a* counts as empty

复杂度分析
时间复杂度：O(mn)O(mn)，其中 mm 和 nn 分别是字符串 ss 和 pp 的长度。我们需要计算出所有的状态，并且每个状态在进行转移时的时间复杂度为 O(1)O(1)。
空间复杂度：O(mn)O(mn)，即为存储所有状态使用的空间。

###### 备忘录递归

```java
class Solution {
    private String pattern;
	private String text;
	private Boolean[][] dp;

	public boolean isMatch(String s, String p) {
    	if(s==null) s=new String();
    	if(p==null) p=new String();
    	//空的正则串不能匹配s
    	if(s.length()!=0&&p.length()==0) return false;
    	//空正则串可以匹配空s
    	if(s.length()==0&&p.length()==0) return true;
    	
    	//非空正则不一定可以匹配空s或非空s
    	//此处由于要处理 * 使用递归解法
    	this.text=s;this.pattern=p;
    	this.dp=new Boolean[s.length()+1][p.length()+1];
    	return recur(0,0);
    }

	//i为text的索引,j为pattern的索引
	private boolean recur(int i, int j) {
		if(j<pattern.length()&&i<text.length()&&dp[i][j]!=null) return dp[i][j];
		//正则串为空
		if(j==pattern.length()) return dp[i][j]=(i==text.length());
		//正则串非空 匹配串为空
		if(i==text.length()) {
			//只可能*取0重才可能为真
			if(j+1<pattern.length()&&pattern.charAt(j+1)=='*') {
				dp[i][j]=recur(i,j+2);//正则串去掉两个
			}else { 
				dp[i][j]=false;
			}
		}else {//正则串非空 匹配串非空
			//当前字符是否匹配
			boolean match=pattern.charAt(j)==text.charAt(i)||pattern.charAt(j)=='.';
			if(j+2<=pattern.length()&&pattern.charAt(j+1)=='*') {
				dp[i][j]=(match&recur(i+1, j))||//当前匹配了,看text的下个字符是否还可以用*匹配
						(recur(i,j+2));//当前不匹配,*取0此重复 
			}else {
				dp[i][j]=match&recur(i+1,j+1);
			}
		}
		return dp[i][j];
	}
}
```

如果不考虑 * 通配符，面对两个待匹配字符 s[i] 和 p[j]，我们唯一能做的就是看他俩是否匹配：

```java
bool isMatch(string s, string p) {
    int i = 0, j = 0;
    while (i < s.size() && j < p.size()) {
        // 「.」通配符就是万金油
        if (s[i] == p[j] || p[j] == '.') {
            // 匹配，接着匹配 s[i+1..] 和 p[j+1..]
            i++; j++;
        } else {
            // 不匹配
            return false;
        }
    }
    return i == j;
}
```


那么考虑一下，如果加入 * 通配符，局面就会稍微复杂一些，不过只要分情况来分析，也不难理解。
当 p[j + 1] 为 * 通配符时，我们分情况讨论下：
1、如果 s[i] == p[j]，那么有两种情况：
	1.1 p[j] 有可能会匹配多个字符，比如 s = "aaa", p = "a*"，那么 p[0] 会通过 * 匹配 3 个字符 "a"。
	1.2 p[i] 也有可能匹配 0 个字符，比如 s = "aa", p = "a*aa"，由于后面的字符可以匹配 s，所以 p[0] 只能匹配 0 次。

2、如果 s[i] != p[j]，只有一种情况：
p[j] 只能匹配 0 次，然后看下一个字符是否能和 s[i] 匹配。比如说 s = "aa", p = "b*aa"，此时 p[0] 只能匹配 0 次。
综上，可以把之前的代码针对 * 通配符进行一下改造：

```java
if (s[i] == p[j] || p[j] == '.') {
    // 匹配
    if (j < p.size() - 1 && p[j + 1] == '*') {
        // 有 * 通配符，可以匹配 0 次或多次
    } else {
        // 无 * 通配符，老老实实匹配 1 次
        i++; j++;
    }
} else {
    // 不匹配
    if (j < p.size() - 1 && p[j + 1] == '*') {
        // 有 * 通配符，只能匹配 0 次
    } else {
        // 无 * 通配符，匹配无法进行下去了
        return false;
    }
}
```

整体的思路已经很清晰了，但现在的问题是，遇到 * 通配符时，到底应该匹配 0 次还是匹配多次？多次是几次？
你看，这就是一个做「选择」的问题，要把所有可能的选择都穷举一遍才能得出结果。动态规划算法的核心就是「状态」和「选择」，「状态」无非就是 i 和 j 两个指针的位置，「选择」就是 p[j] 选择匹配几个字符。
根据「状态」，我们可以定义一个 dp 函数：

```java
bool dp(string& s, int i, string& p, int j);
```

dp 函数的定义如下：
若 dp(s, i, p, j) = true，则表示 s[i..] 可以匹配 p[j..]；若 dp(s, i, p, j) = false，则表示 s[i..] 无法匹配 p[j..]。
根据这个定义，我们想要的答案就是 i = 0, j = 0 时 dp 函数的结果，所以可以这样使用这个 dp 函数：

```java
bool isMatch(string s, string p) {
    // 指针 i，j 从索引 0 开始移动
    return dp(s, 0, p, 0);
可以根据之前的代码写出 dp 函数的主要逻辑：

bool dp(string& s, int i, string& p, int j) {
    if (s[i] == p[j] || p[j] == '.') {
        // 匹配
        if (j < p.size() - 1 && p[j + 1] == '*') {
            // 1.1 通配符匹配 0 次或多次
            return dp(s, i, p, j + 2)
                || dp(s, i + 1, p, j);
        } else {
            // 1.2 常规匹配 1 次
            return dp(s, i + 1, p, j + 1);
        }
    } else {
        // 不匹配
        if (j < p.size() - 1 && p[j + 1] == '*') {
            // 2.1 通配符匹配 0 次
            return dp(s, i, p, j + 2);
        } else {
            // 2.2 无法继续匹配
            return false;
        }
    }
}
```

根据 dp 函数的定义，这几种情况都很好解释：

将 j 加 2，i 不变，含义就是直接跳过 p[j] 和之后的通配符，即通配符匹配 0 次：

![img](https://pic.leetcode-cn.com/1601009810-SPnfKy-file_1601009810046)

将 i 加 1，j 不变，含义就是 p[j] 匹配了 s[i]，但 p[j] 还可以继续匹配，即通配符匹配多次的情况：

![img](https://pic.leetcode-cn.com/1601009809-plieNN-file_1601009809876)

两种情况只要有一种可以完成匹配即可，所以对上面两种情况求或运算。

1.2 常规匹配 1 次

由于这个条件分支是无 * 的常规匹配，那么如果 s[i] == p[j]，就是 i 和 j 分别加一：

![img](https://pic.leetcode-cn.com/1601009810-KOQMGT-file_1601009810013)

2.1 通配符匹配 0 次

类似情况 1.1，将 j 加 2，i 不变：

![img](https://pic.leetcode-cn.com/1601009810-SPnfKy-file_1601009810046)

2.2 如果没有 * 通配符，也无法匹配，那只能说明匹配失败了：

![img](https://pic.leetcode-cn.com/1601009810-YMvkKp-file_1601009810313)

看图理解应该很容易了，现在可以思考一下 dp 函数的 base case：

一个 base case 是 j == p.size() 时，按照 dp 函数的定义，这意味着模式串 p 已经被匹配完了，那么应该看看文本串 s 匹配到哪里了，如果 s 也恰好被匹配完，则说明匹配成功：

```java
if (j == p.size()) {
    return i == s.size();
}
```


另一个 base case 是 i == s.size() 时，按照 dp 函数的定义，这种情况意味着文本串 s 已经全部被匹配了，那么是不是只要简单地检查一下 p 是否也匹配完就行了呢？

```javajava
if (i == s.size()) {
    // 这样行吗？
    return j == p.size();
}
```


这是不正确的，此时并不能根据 j 是否等于 p.size() 来判断是否完成匹配，只要 p[j..] 能够匹配空串，就可以算完成匹配。比如说 s = "a", p = "ab*c*"，当 i 走到 s 末尾的时候，j 并没有走到 p 的末尾，但是 p 依然可以匹配 s。

所以我们可以写出如下代码：

```java
int m = s.size(), n = p.size();

if (i == s.size()) {
    // 如果能匹配空串，一定是字符和 * 成对儿出现
    if ((n - j) % 2 == 1) {
        return false;
    }
    // 检查是否为 x*y*z* 这种形式
    for (; j + 1 < p.size(); j += 2) {
        if (p[j + 1] != '*') {
            return false;
        }
    }
    return true;
}
```


根据以上思路，就可以写出完整的代码：

```java
/* 计算 p[j..] 是否匹配 s[i..] */
bool dp(string& s, int i, string& p, int j) {
    int m = s.size(), n = p.size();
    // base case
    if (j == n) {
        return i == m;
    }
    if (i == m) {
        if ((n - j) % 2 == 1) {
            return false;
        }
        for (; j + 1 < n; j += 2) {
            if (p[j + 1] != '*') {
                return false;
            }
        }
        return true;
    }

    // 记录状态 (i, j)，消除重叠子问题
    string key = to_string(i) + "," + to_string(j);
    if (memo.count(key)) return memo[key];
    
    bool res = false;
    
    if (s[i] == p[j] || p[j] == '.') {
        if (j < n - 1 && p[j + 1] == '*') {
            res = dp(s, i, p, j + 2)
               || dp(s, i + 1, p, j);
        } else {
            res = dp(s, i + 1, p, j + 1);
        }
    } else {
        if (j < n - 1 && p[j + 1] == '*') {
            res = dp(s, i, p, j + 2);
        } else {
            res = false;
        }
    }
    // 将当前结果记入备忘录
    memo[key] = res;
    
    return res;
}
```

代码中用了一个哈希表 memo 消除重叠子问题，因为正则表达算法的递归框架如下：

```java
bool dp(string& s, int i, string& p, int j) {
    dp(s, i, p, j + 2);     // 1
    dp(s, i + 1, p, j);     // 2
    dp(s, i + 1, p, j + 1); // 3
}
```


那么，如果让你从 dp(s, i, p, j) 得到 dp(s, i+2, p, j+2)，至少有两条路径：1 -> 2 -> 2 和 3 -> 3，那么就说明 (i+2, j+2) 这个状态存在重复，这就说明存在重叠子问题。

动态规划的时间复杂度为「状态的总数」*「每次递归花费的时间」，本题中状态的总数当然就是 i 和 j 的组合，也就是 M * N（M 为 s 的长度，N 为 p 的长度）；递归函数 dp 中没有循环（base case 中的不考虑，因为 base case 的触发次数有限），所以一次递归花费的时间为常数。二者相乘，总的时间复杂度为 O(MN)。

###### 生成有限状态机

```java
public class Lc10 {

    public boolean isMatch(String s, String p) {
        char[] sc = s.toCharArray();
        char[] pc = (p + ")").toCharArray();

        int m = pc.length + 1;
        boolean[][] graph = new boolean[m][m];
        for (int i = 0; i < m - 1; i++) {
            if (pc[i] == '*') {
                graph[i - 1][i] = true;
                graph[i][i - 1] = true;
                graph[i][i + 1] = true;
            } else if (pc[i] == ')') {
                graph[i][i + 1] = true;
            }
        }

        boolean[] mark = dfs(graph, 0);
        for (int i = 0; i < sc.length; i++) {
            boolean[] match = new boolean[m];
            for (int j = 0; j < m; j++) {
                if (mark[j]) {
                    if (j == m - 1) {
                        continue;
                    }

                    if (pc[j] == sc[i] || pc[j] == '.') {
                        match[j + 1] = true;
                    }
                }
            }

            mark = dfs(graph, match);
        }

        return mark[m - 1];
    }

    private boolean[] dfs(boolean[][] graph, int s) {
        boolean[] re = new boolean[graph.length];
        doDfs(graph, re, s);

        return re;
    }

    private boolean[] dfs(boolean[][] graph, boolean[] mark) {
        boolean[] re = new boolean[graph.length];
        for (int i = 0; i < mark.length; i++) {
            if (mark[i]) {
                doDfs(graph, re, i);
            }
        }

        return re;
    }

    private void doDfs(boolean[][] graph, boolean[] mark, int v) {
        mark[v] = true;
        for (int i = 0; i < graph.length; i++) {
            if (!mark[i] && graph[v][i]) {
                doDfs(graph, mark, i);
            }
        }
    }
}
```

解题思路：
一步到位直接按正则表达式的实现来:生成有限状态机

状态机节点设计:
该节点字符
子孙节点 map
本节点是否为最终节点
本节点的最小长度，如果后面携带 *∗，则自由长度为 00; 否则为 11

生成的有限状态机图, 使用 `mermaid` 去查看。如下用例生成的状态机 `c*..b*a*a.*a..*c` 与 `mis*is*`

![img](https://pic.leetcode-cn.com/f4d2b6427ee8f13929f229c39d28b23c0516e97d3b78f2ef1e0264433effc287-Screen%20Shot%202019-06-01%20at%2013.41.41.png)