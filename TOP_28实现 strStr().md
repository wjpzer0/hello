#### 题目

实现 strStr() 函数。
给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:
输入: haystack = "hello", needle = "ll"
输出: 2

示例 2:
输入: haystack = "aaaaa", needle = "bba"
输出: -1

说明:
当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

#### 我的解法

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        if len(needle) == 0:
            return 0
        needlen = len(needle)
        i = 0
        while (i + needlen - 1) < len(haystack):
            if haystack[i: i+needlen] == needle:
                return i
            i += 1
        else:
            return -1

```

#### 其他解法

###### 子串逐一比较 - 线性时间复杂度

最直接的方法 - 沿着字符换逐步移动滑动窗口，将窗口内的子串与 needle 字符串比较。

![fig](https://pic.leetcode-cn.com/Figures/28/substrings.png)

```java
class Solution {
  public int strStr(String haystack, String needle) {
    int L = needle.length(), n = haystack.length();

    for (int start = 0; start < n - L + 1; ++start) {
      if (haystack.substring(start, start + L).equals(needle)) {
        return start;
      }
    }
    return -1;
  }
```

**复杂度分析**
时间复杂度：O((N - L)L)O((N−L)L)，其中 N 为 haystack 字符串的长度，L 为 needle 字符串的长度。内循环中比较字符串的复杂度为 L，总共需要比较 (N - L) 次。
空间复杂度：O(1)O(1)。

###### 双指针 - 线性时间复杂度

上一个方法的缺陷是会将 haystack 所有长度为 L 的子串都与 needle 字符串比较，实际上是不需要这么做的。
首先，只有子串的第一个字符跟 needle 字符串第一个字符相同的时候才需要比较。
![fig](https://pic.leetcode-cn.com/Figures/28/two_pointers_start2.png)
其次，可以一个字符一个字符比较，一旦不匹配了就立刻终止。
![fig](https://pic.leetcode-cn.com/Figures/28/mismatch2.png)
如下图所示，比较到最后一位时发现不匹配，这时候开始回溯。需要注意的是，pn 指针是移动到 pn = pn - curr_len + 1 的位置，而 不是 pn = pn - curr_len 的位置。
![fig](https://pic.leetcode-cn.com/Figures/28/backtrack2.png)
这时候再比较一次，就找到了完整匹配的子串，直接返回子串的开始位置 pn - L。
![fig](https://pic.leetcode-cn.com/Figures/28/match.png)
**算法**
移动 pn 指针，直到 pn 所指向位置的字符与 needle 字符串第一个字符相等。
通过 pn，pL，curr_len 计算匹配长度。
如果完全匹配（即 curr_len == L），返回匹配子串的起始坐标（即 pn - L）。
如果不完全匹配，回溯。使 pn = pn - curr_len + 1， pL = 0， curr_len = 0。

```java
class Solution {
  public int strStr(String haystack, String needle) {
    int L = needle.length(), n = haystack.length();
    if (L == 0) return 0;

    int pn = 0;
    while (pn < n - L + 1) {
      // 找到第一个针字符的位置在haystack字符串中
      while (pn < n - L + 1 && haystack.charAt(pn) != needle.charAt(0)) ++pn;

      // 计算最大匹配字符串
      int currLen = 0, pL = 0;
      while (pL < L && pn < n && haystack.charAt(pn) == needle.charAt(pL)) {
        ++pn;
        ++pL;
        ++currLen;
      }

      // 如果找到了整个字符串needle
      // 返回起始位置
      if (currLen == L) return pn - L;

      // 否则,放弃
      pn = pn - currLen + 1;
    }
    return -1;
  }
}
```

**复杂度分析**
时间复杂度：最坏时间复杂度为 O((N - L)L)，最优时间复杂度为 O(N)。
空间复杂度：O(1)。

######  Rabin Karp - 常数复杂度

有一种最坏时间复杂度也为 O(N)的算法。思路是这样的，先生成窗口内子串的哈希码，然后再跟 needle 字符串的哈希码做比较。

这个思路有一个问题需要解决，如何在常数时间生成子串的哈希码？

滚动哈希：常数时间生成哈希码

生成一个长度为 L 数组的哈希码，需要 O(L) 时间。

如何在常数时间生成滑动窗口数组的哈希码？利用滑动窗口的特性，每次滑动都有一个元素进，一个出。

由于只会出现小写的英文字母，因此可以将字符串转化成值为 0 到 25 的整数数组： arr[i] = (int)S.charAt(i) - (int)'a'。按照这种规则，abcd 整数数组形式就是 [0, 1, 2, 3]，转换公式如下所示。

*h*0=0×26^3+1×26^2+2×26^1+3×26^0

可以将上面的公式写成通式，如下所示。其中 c_i为整数数组中的元素，a = 26，其为字符集的个数。

h0 =c0a^(L−1)+c1a ^(L−2)+...+cia^(L−1−i)+...+c(L−1)a ^1+cLa^0

下面来考虑窗口从 abcd 滑动到 bcde 的情况。这时候整数形式数组从 [0, 1, 2, 3] 变成了 [1, 2, 3, 4]，数组最左边的 0 被移除，同时最右边新添了 4。滑动后数组的哈希值可以根据滑动前数组的哈希值来计算，计算公式如下所示。

*h*1=(*h*0−0×26^3)×26+4×26^0

写成通式如下所示。

*h*1=(*h*0a−*c*0a^L)+c^(L+1)

**如何避免溢出**
a^L可能是一个很大的数字，因此需要设置数值上限来避免溢出。设置数值上限可以用取模的方式，即用 h % modulus 来代替原本的哈希值。
理论上，modules 应该取一个很大数，但具体应该取多大的数呢? 详见这篇文章，对于这个问题来说 2^31就足够了。

**算法**
计算子字符串 haystack.substring(0, L) 和 needle.substring(0, L) 的哈希值。
​	从起始位置开始遍历：从第一个字符遍历到第 N - L 个字符。
​	根据前一个哈希值计算滚动哈希。
如果子字符串哈希值与 needle 字符串哈希值相等，返回滑动窗口起始位置。
返回 -1，这时候 haystack 字符串中不存在 needle 字符串。

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        L, n = len(needle), len(haystack)
        if L > n:
            return -1
        
        # base value for the rolling hash function
        a = 26
        # modulus value for the rolling hash function to avoid overflow
        modulus = 2**31
        
        # lambda-function to convert character to integer
        h_to_int = lambda i : ord(haystack[i]) - ord('a')
        needle_to_int = lambda i : ord(needle[i]) - ord('a')
        
        # compute the hash of strings haystack[:L], needle[:L]
        h = ref_h = 0
        for i in range(L):
            h = (h * a + h_to_int(i)) % modulus
            ref_h = (ref_h * a + needle_to_int(i)) % modulus
        if h == ref_h:
            return 0
              
        # const value to be used often : a**L % modulus
        aL = pow(a, L, modulus) 
        for start in range(1, n - L + 1):
            # compute rolling hash in O(1) time
            h = (h * a - h_to_int(start - 1) * aL + h_to_int(start + L - 1)) % modulus
            if h == ref_h:
                return start

        return -1
```

**复杂度分析**
时间复杂度：O(N)O(N)，计算 needle 字符串的哈希值需要 O(L)O(L) 时间，之后需要执行 (N - L)(N−L) 次循环，每次循环的计算复杂度为常数。
空间复杂度：O(1)O(1)。

###### KMP 算法详解

​		先在开头约定，本文用 pat 表示模式串，长度为 M，txt 表示文本串，长度为 N。KMP 算法是在 txt 中查找子串 pat，如果存在，返回这个子串的起始索引，否则返回 -1。
​		本文则用一个二维的 `dp` 数组（但空间复杂度还是 O(M)），重新定义其中元素的含义，使得代码长度大大减少，可解释性大大提高。
​		KMP 算法永不回退 txt 的指针 i，不走回头路（不会重复扫描 txt），而是借助 dp 数组中储存的信息把 pat 移到正确的位置继续匹配，时间复杂度只需 O(N)，用空间换时间。
​		计算这个 dp 数组，只和 pat 串有关。意思是说，只要给我个 pat，我就能通过这个模式串计算出 dp 数组，然后你可以给我不同的 txt，我都不怕，利用这个 dp 数组我都能在 O(N) 时间完成字符串匹配。

具体来说，比如上文举的两个例子：

```java
txt1 = "aaacaaab" 
pat = "aaab"
txt2 = "aaaaaaab" 
pat = "aaab"
```


我们的 txt 不同，但是 pat 是一样的，所以 KMP 算法使用的 dp 数组是同一个。

只不过对于 txt1 的下面这个即将出现的未匹配情况：

![img](https://pic.leetcode-cn.com/18ba3fd7bbc86730f0beb7b9e7c4fed636d9f940640a9171bc82b9c740666a44-file_1568963022990)

dp 数组指示 pat 这样移动：

![img](https://pic.leetcode-cn.com/170351488003bbeea650e7ebe4daddd8533cd6da7d0b62c49d886c3d959c4c53-file_1568963023003)

PS：这个j 不要理解为索引，它的含义更准确地说应该是状态（state），所以它会出现这个奇怪的位置，后文会详述。

而对于 txt2 的下面这个即将出现的未匹配情况：

![img](https://pic.leetcode-cn.com/34aafbed3f7ac19bb9d5848872709a9f152bc919efc44fd0521ca7bde4d371b6-file_1568963023006)

dp 数组指示 pat 这样移动：

![img](https://pic.leetcode-cn.com/d382508fe796c2e5fc6f6f1705b7d242b33c91036cc2908f158f0e0715698fc5-file_1568963023012)

明白了 dp 数组只和 pat 有关，那么我们这样设计 KMP 算法就会比较漂亮：

```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        // 通过 pat 构建 dp 数组
        // 需要 O(M) 时间
    }

    public int search(String txt) {
        // 借助 dp 数组去匹配 txt
        // 需要 O(N) 时间
    }
}

```


这样，当我们需要用同一 pat 去匹配不同 txt 时，就不需要浪费时间构造 dp 数组了：

```java
KMP kmp = new KMP("aaab");
int pos1 = kmp.search("aaacaaab"); //4
int pos2 = kmp.search("aaaaaaab"); //4
```

KMP 算法最关键的步骤就是构造这个状态转移图。要确定状态转移的行为，得明确两个变量，一个是当前的匹配状态，另一个是遇到的字符；确定了这两个变量后，就可以知道这个情况下应该转移到哪个状态。

下面看一下 KMP 算法根据这幅状态转移图匹配字符串 txt 的过程：

![img](https://pic.leetcode-cn.com/74666053023d668f2007c84382a86930c1270807cdf2f4394165bf2b16336f72-file_1568963023167)

请记住这个 GIF 的匹配过程，这就是 KMP 算法的核心逻辑

为了描述状态转移图，我们定义一个二维 dp 数组，它的含义如下：

```java
dp[j][c] = next
0 <= j < M，代表当前的状态
0 <= c < 256，代表遇到的字符（ASCII 码）
0 <= next <= M，代表下一个状态

dp[4]['A'] = 3 表示：
当前是状态 4，如果遇到字符 A，
pat 应该转移到状态 3

dp[1]['B'] = 2 表示：
当前是状态 1，如果遇到字符 B，
pat 应该转移到状态 2
```

根据我们这个 dp 数组的定义和刚才状态转移的过程，我们可以先写出 KMP 算法的 search 函数代码：

```java
public int search(String txt) {
    int M = pat.length();
    int N = txt.length();
    // pat 的初始态为 0
    int j = 0;
    for (int i = 0; i < N; i++) {
        // 当前是状态 j，遇到字符 txt[i]，
        // pat 应该转移到哪个状态？
        j = dp[j][txt.charAt(i)];
        // 如果达到终止态，返回匹配开头的索引
        if (j == M) return i - M + 1;
    }
    // 没到达终止态，匹配失败
    return -1;
}
```

回想刚才说的：要确定状态转移的行为，必须明确两个变量，一个是当前的匹配状态，另一个是遇到的字符，而且我们已经根据这个逻辑确定了 dp 数组的含义，那么构造 dp 数组的框架就是这样：

```Java
for 0 <= j < M: # 状态
    for 0 <= c < 256: # 字符
        dp[j][c] = next
```

这个 next 状态应该怎么求呢？显然，如果遇到的字符 c 和 pat[j] 匹配的话，状态就应该向前推进一个，也就是说 next = j + 1，我们不妨称这种情况为状态推进：

![img](https://pic.leetcode-cn.com/b24c0df2e6f2ecf0263ae89778c39c6f37b7c8538cc554a9d093e5f8b2df441a-file_1568963023142)

如果字符 c 和 pat[j] 不匹配的话，状态就要回退（或者原地不动），我们不妨称这种情况为状态重启：

![img](https://pic.leetcode-cn.com/08eeeedc6bc8b75657802180c7827758b397f6787d65b8b79fdfa82e42e3524c-file_1568963023148)

那么，如何得知在哪个状态重启呢？解答这个问题之前，我们再定义一个名字：影子状态（我编的名字），用变量 X 表示。所谓影子状态，就是和当前状态具有相同的前缀。比如下面这种情况：

![img](https://pic.leetcode-cn.com/a8744e52bd2e20fac0316cdebee2cf35c0accbe6cb65968279273401a2f12468-file_1568963023150)

当前状态 j = 4，其影子状态为 X = 2，它们都有相同的前缀 "AB"。因为状态 X 和状态 j 存在相同的前缀，所以当状态 j 准备进行状态重启的时候（遇到的字符 c 和 pat[j] 不匹配），可以通过 X 的状态转移图来获得最近的重启位置。
比如说刚才的情况，如果状态 j 遇到一个字符 "A"，应该转移到哪里呢？首先只有遇到 "C" 才能推进状态，遇到 "A" 显然只能进行状态重启。状态 j 会把这个字符委托给状态 X 处理，也就是 dp[j]\['A'] = dp[X]\['A']：

![img](https://pic.leetcode-cn.com/bbdf82f217e9dcccdf99d62b13ff7bd918412c570de5afd842d31646dd5970c1-file_1568963023151)

因为：既然 j 这边已经确定字符 "A" 无法推进状态，只能回退，而且 KMP 就是要尽可能少的回退，以免多余的计算。那么 j 就可以去问问和自己具有相同前缀的 X，如果 X 遇见 "A" 可以进行「状态推进」，那就转移过去，因为这样回退最少。

![img](https://pic.leetcode-cn.com/16a59300a61344c1bc11e470cfb4430948b4b8936d788abbe45a452e58feeacf-file_1568963023160)

当然，如果遇到的字符是 "B"，状态 `X` 也不能进行「状态推进」，只能回退，`j` 只要跟着 `X` 指引的方向回退就行了：

![img](https://pic.leetcode-cn.com/a1b042a21cafcee0275df791bb105b1540e66a96d25e9adc6aa1ebc1e3befebd-file_1568963023162)

这样，我们就细化一下刚才的框架代码：

```java
int X # 影子状态
for 0 <= j < M:
    for 0 <= c < 256:
        if c == pat[j]:
            # 状态推进
            dp[j][c] = j + 1
        else: 
            # 状态重启
            # 委托 X 计算重启位置
            dp[j][c] = dp[X][c] 
```

```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[状态][字符] = 下个状态
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // 影子状态 X 初始为 0
        int X = 0;
        // 当前状态 j 从 1 开始
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++) {
                if (pat.charAt(j) == c) 
                    dp[j][c] = j + 1;
                else 
                    dp[j][c] = dp[X][c];
            }
            // 更新影子状态
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {...}
}
```

先解释一下这一行代码：

```java
// base case
dp[0][pat.charAt(0)] = 1;
```

这行代码是 base case，只有遇到 pat[0] 这个字符才能使状态从 0 转移到 1，遇到其它字符的话还是停留在状态 0（Java 默认初始化数组全为 0）。
影子状态 X 是先初始化为 0，然后随着 j 的前进而不断更新的。下面看看到底应该如何更新影子状态 X：

```java
int X = 0;
for (int j = 1; j < M; j++) {
    ...
    // 更新影子状态
    // 当前是状态 X，遇到字符 pat[j]，
    // pat 应该转移到哪个状态？
    X = dp[X][pat.charAt(j)];
}
```

更新 X 其实和 search 函数中更新状态 j 的过程是非常相似的：

```java
int j = 0;
for (int i = 0; i < N; i++) {
    // 当前是状态 j，遇到字符 txt[i]，
    // pat 应该转移到哪个状态？
    j = dp[j][txt.charAt(i)];
    ...
}
```

其中的原理非常微妙，注意代码中 for 循环的变量初始值，可以这样理解：后者是在 txt 中匹配 pat，前者是在 pat 中匹配 pat[1..end]，状态 X 总是落后状态 j 一个状态，与 j 具有最长的相同前缀。所以我把 X 比喻为影子状态，似乎也有一点贴切。
另外，构建 dp 数组是根据 base case dp[0]\[..] 向后推演。这就是我认为 KMP 算法就是一种动态规划算法的原因。
下面来看一下状态转移图的完整构造过程，你就能理解状态 X 作用之精妙了：

![img](https://pic.leetcode-cn.com/b3fe29e2c772df9eb04baae42b4be0a388d03a625cb3815de474868531768d9b-file_1568963023183)

```java
public class KMP {
    private int[][] dp;
    private String pat;

    public KMP(String pat) {
        this.pat = pat;
        int M = pat.length();
        // dp[状态][字符] = 下个状态
        dp = new int[M][256];
        // base case
        dp[0][pat.charAt(0)] = 1;
        // 影子状态 X 初始为 0
        int X = 0;
        // 构建状态转移图（稍改的更紧凑了）
        for (int j = 1; j < M; j++) {
            for (int c = 0; c < 256; c++) {
                dp[j][c] = dp[X][c];
            dp[j][pat.charAt(j)] = j + 1;
            // 更新影子状态
            X = dp[X][pat.charAt(j)];
        }
    }

    public int search(String txt) {
        int M = pat.length();
        int N = txt.length();
        // pat 的初始态为 0
        int j = 0;
        for (int i = 0; i < N; i++) {
            // 计算 pat 的下一个状态
            j = dp[j][txt.charAt(i)];
            // 到达终止态，返回结果
            if (j == M) return i - M + 1;
        }
        // 没到达终止态，匹配失败
        return -1;
    }
}
```

```c
#define MAP_NULL -854578421

typedef struct node Node;
struct node
{
    int key;
    int value;
    int hash;
    Node* next;
};

typedef struct hash_map
{
    int size;
    int length;
    Node** data;
}HashMap;

/*
    扩容
*/
void resize(HashMap* hashMap)
{
    int oldSize = hashMap->size;
    int newSize = oldSize * 2;
    Node** newCup = (Node**)malloc(sizeof(Node*) * newSize);
    memset(newCup, 0, sizeof(Node*) * newSize);
    //拷贝到老数组
    for (int j = 0, size = hashMap->size; j < size; j++)
    {
        Node* e = hashMap->data[j];
        hashMap->data[j] = NULL;
        if (e == NULL)
        {
            continue;
        }
        if (e->next == NULL)
        {
            newCup[e->hash & (newSize - 1)] = e;
        }
        Node* loTail = NULL;
        Node* hiTail = NULL;
        Node* loHead = NULL;
        Node* hiHead = NULL;
        Node* next;
        do
        {
            next = e->next;
            //放在高位
            if ((e->hash & oldSize) != 0)
            {
                if (hiTail == NULL)
                {
                    hiHead = e;
                }
                else
                {
                    hiTail->next = e;
                }
                hiTail = e;
            }
            else
            {
                if (loHead == NULL)
                {
                    loHead = e;
                }
                else
                {
                    loTail->next = e;
                }
                loTail = e;
            }
        } while ((e = next) != NULL);
        newCup[j + oldSize] = hiHead;
        newCup[j] = loHead;
        if (hiTail != NULL)
            hiTail->next = NULL;
        if (loTail != NULL)
            loTail->next = NULL;
    }
    free(hashMap->data);
    hashMap->data = newCup;
    hashMap->size = newSize;
}

Node* createNode(int hash, int key, int value, Node* next)
{
    Node* node = (Node*)malloc(sizeof(Node));
    node->hash = hash;
    node->key = key;
    node->value = value;
    node->next = NULL;
    return node;
}

void FREE0(Node* node)
{
    if (node == NULL)
    {
        return;
    }
    FREE0(node->next);
    free(node);
}

/*
    free
*/
void free_map(HashMap* hashMap)
{
    for (int i = 0, size = hashMap->size; i < size; i++)
    {
        Node* e = hashMap->data[i];
        if (e != NULL && e->next == NULL)
        {
            free(e);
        }
        else
        {
            FREE0(e);
        }
    }
    free(hashMap);
}

/*
    get
*/
int get(HashMap* hashMap, int key)
{
    int hash = key;
    int index = (hashMap->size - 1) & hash;
    Node* node = hashMap->data[index];
    if (node == NULL)
    {
        return MAP_NULL;
    }
    if (node->next == NULL)
    {
        return node->value;
    }
    Node* next;
    do
    {
        next = node->next;
        if (node->key == key)
        {
            return node->value;
        }
    } while ((node = next) != NULL);
    return MAP_NULL;
}

/*
    contains
*/
int contains(HashMap* hashMap, int key)
{
    int hash = key;
    int index = (hashMap->size - 1) & hash;
    Node* node = hashMap->data[index];
    if (node == NULL)
    {
        return 0;
    }
    if (node->next == NULL)
    {
        if (node->key == key)
            return 1;
        else
            return 0;
    }
    Node* next;
    do
    {
        next = node->next;
        if (node->key == key)
        {
            return 1;
        }
    } while ((node = next) != NULL);
    return 0;
}

/*
    新增
*/
void put(int key, int value, HashMap* hashMap)
{
    if (hashMap == NULL)
    {
        return;
    }
    if (hashMap->length == hashMap->size * 0.75)
    {
        resize(hashMap);
    }
    int hash = key;
    int index = hash & (hashMap->size - 1);
    Node** nodeIndex = &hashMap->data[index];
    //如果该槽位无元素则直接插入
    if (*nodeIndex == NULL)
    {
        *nodeIndex = createNode(hash, key, value, NULL);
        hashMap->length++;
        return;
    }
    Node* e = *nodeIndex;
    Node* next = e;
    do
    {
        e = next;
        next = e->next;
        if (e->key == key)
        {
            e->value = value;
            return;
        }
    } while (next != NULL);
    e->next = createNode(hash, key, value, NULL);
    hashMap->length++;
}

/*
    创建hashMap
*/
HashMap* newHashMap()
{
    HashMap* hashMap = (HashMap*)malloc(sizeof(HashMap));
    hashMap->size = 16;
    hashMap->length = 0;
    hashMap->data = (Node**)malloc(sizeof(Node*) * hashMap->size);
    memset(hashMap->data, 0, sizeof(Node*) * hashMap->size);
    return hashMap;
}

/*
    计算next数组
*/
int* get_next_array(char* needle,int target_len)
{
    int* next = (int*)malloc(sizeof(int) * strlen(needle));
    int i = 1;
    next[0] = 0;
    //创建map
    HashMap* hash_nap = newHashMap();
    while (needle[i])
    {
        int value = get(hash_nap, needle[i]);
        if (value == MAP_NULL)
            next[i] = i;
        else
            next[i] = i - value;
        put(needle[i], i, hash_nap);
        i++;
    }
    // for (int i = 0; i < target_len; i++)
    // {
    //     printf("%d ",next[i]);
    // }
    free_map(hash_nap);
    return next;
}

int strStr(char * haystack, char * needle)
{
    int haystack_len = (int)strlen(haystack);
    int needle_len = (int)strlen(needle);
    if (!needle_len)
        return 0;
    if (!haystack_len)
        return -1;
    //KMP算法
    int* next = get_next_array(needle,needle_len);
    int p1 = 0;
    int p2 = 0;
    int p1_bak = 0;
    int flag = 0;
    while (p1 < haystack_len && p2 < needle_len)
    {
        if (p2 == needle_len)
        {
            flag = p1_bak;
            break;
        }
        if (haystack[p1] == needle[p2])
        {
            p1++;
            p2++;
            continue;
        }
        if(p2 == 0)
            p1 = p1_bak + 1;
        else
            p1 = p1_bak + (next[p2 - 1] == 0 ? 1 : next[p2 - 1]);
        p1_bak = p1;
        p2 = 0;
    }
    return p2 == needle_len ? flag = p1_bak : -1;
}
```

