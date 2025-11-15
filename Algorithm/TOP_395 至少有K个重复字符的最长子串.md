# 题目

给你一个字符串 s 和一个整数 k ，请你找出 s 中的最长子串， 要求该子串中的每一字符出现次数都不少于 k 。返回这一子串的长度。

示例 1：

```
输入：s = "aaabb", k = 3
输出：3
解释：最长子串为 "aaa" ，其中 'a' 重复了 3 次。
```


示例 2：

```
输入：s = "ababbc", k = 2
输出：5
解释：最长子串为 "ababb" ，其中 'a' 重复了 2 次， 'b' 重复了 3 次。
```


提示：

- 1 <= s.length <= $10^4$
- s 仅由小写英文字母组成
- 1 <= k <= $10^5$

## 我的解法

未解出，无法理顺思路。

## 其他解法

### 分治

```python
# 分治：对于一个字符串来说，如果要求子串最少出现k次，那么如果某些字母出现的次数小于k,
# 这些字母一定不会出现在最长的子串中，并且这些字母将整个字符子串分割成小段，这些小段有可能是最长的
# 但是由于被分割了，还是要检查这一小段，如果某些字母出现的次数小于k,会将小段继续分割下去,
# 比如字符串"aacbbbdc"，要求最少出现2次,我们记录左右闭区间，，
# 第一轮[0,7]，处理"aacbbbdc"，d只出现了一次不满足，于是递归解决区间[0,5]、[7,7]
# 第二轮[0,5]，处理"aacbbb"，  c只出现了一次不满足，于是递归解决区间[0,1]、[3,4] 
# 第二轮[7,7]，处理"c"，       c只出现了一次不满足，不继续递归
# 第三轮[0,1]，处理"aa"，      满足出现次数>=2,ret=2
# 第三轮[3,4]，处理"bbb"，     满足出现次数>=2 ret=3;
class Solution:
    def longestSubstring(self, s: str, k: int) -> int:
        if not s:
            return 0 # 如果s为空字符长，那么返回长度0
        for c in set(s# 
            if s.count(c)<k:
                s = s.replace(c,'#')
            if '#' in s:
                return max([self.longestSubstring(t,k) for t in s.split('#')])
            # 如果字符串中存在数量小于k的字符，那么该字符串必不合格，按照个数小于k的字符划分字符串，对划分的字符串继续递归判断
            
        return len(s) # 如果s中所有字符个数都大于k，返回s的长度
```

```Java
class Solution {
    public int longestSubstring(String s, int k) {
        int n = s.length();
        return dfs(s, 0, n - 1, k);
    }

    public int dfs(String s, int l, int r, int k) {
        int[] cnt = new int[26];
        for (int i = l; i <= r; i++) {
            cnt[s.charAt(i) - 'a']++;
        }

        char split = 0;
        for (int i = 0; i < 26; i++) {
            if (cnt[i] > 0 && cnt[i] < k) {
                split = (char) (i + 'a');
                break;
            }
        }
        if (split == 0) {
            return r - l + 1;
        }

        int i = l;
        int ret = 0;
        while (i <= r) {
            while (i <= r && s.charAt(i) == split) {
                i++;
            }
            if (i > r) {
                break;
            }
            int start = i;
            while (i <= r && s.charAt(i) != split) {
                i++;
            }

            int length = dfs(s, start, i - 1, k);
            ret = Math.max(ret, length);
        }
        return ret;
    }
}
```

对于字符串 ss，如果存在某个字符ch，它的出现次数大于 0 且小于 k，则任何包含 ch 的子串都不可能满足要求。也就是说，我们将字符串按照 ch 切分成若干段，则满足要求的最长子串一定出现在某个被切分的段内，而不能跨越一个或多个段。因此，可以考虑分治的方式求解本题。

#### 复杂度分析

时间复杂度：O(N⋅∣Σ∣)，其中 N 为字符串的长度，Σ 为字符集，本题中字符串仅包含小写字母，因此 ∣Σ∣=26。由于每次递归调用都会完全去除某个字符，因此递归深度最多为 ∣Σ∣。

空间复杂度：$O(|\Sigma|^2)$。递归的深度为 O(∣Σ∣)，每层递归需要开辟 O(∣Σ∣) 的额外空间。

### 滑动窗口

```java
class Solution {
    public int longestSubstring(String s, int k) {
        int ret = 0;
        int n = s.length();
        for (int t = 1; t <= 26; t++) {
            int l = 0, r = 0;
            int[] cnt = new int[26];
            int tot = 0;
            int less = 0;
            while (r < n) {
                cnt[s.charAt(r) - 'a']++;
                if (cnt[s.charAt(r) - 'a'] == 1) {
                    tot++;
                    less++;
                }
                if (cnt[s.charAt(r) - 'a'] == k) {
                    less--;
                }

                while (tot > t) {
                    cnt[s.charAt(l) - 'a']--;
                    if (cnt[s.charAt(l) - 'a'] == k - 1) {
                        less++;
                    }
                    if (cnt[s.charAt(l) - 'a'] == 0) {
                        tot--;
                        less--;
                    }
                    l++;
                }
                if (less == 0) {
                    ret = Math.max(ret, r - l + 1);
                }
                r++;
            }
        }
        return ret;
    }
}
```

我们枚举最长子串中的字符种类数目，它最小为 1，最大为 ∣Σ∣（字符集的大小，本题中为 26）。

对于给定的字符种类数量 t，我们维护滑动窗口的左右边界 l,r 滑动窗口内部每个字符出现的次数 cnt，以及滑动窗口内的字符种类数目 total。当 total>t 时，我们不断地右移左边界 l，并对应地更新 cnt 以及 total，直到 total≤t 为止。这样，对于任何一个右边界 r，我们都能找到最小的 l（记为$ l_{min}$，使得 $s[l_{min}...r]$之间的字符种类数目不多于 t。

对于任何一组 $l_{min}$, r 而言，如果 $s[l_{min}...r]$之间存在某个出现次数小于 k （且不为 0，下文不再特殊说明）的字符，我们可以断定：对于任何$ l' \in (l_{min}, r)$而言，$s[l'...r]$依然不可能是满足题意的子串，因为：

- 要么该字符的出现次数降为 0，此时子串内虽然少了一个出现次数小于 k 的字符，但字符种类数目也随之小于 t 了；
- 要么该字符的出现次数降为非 0 整数，此时该字符的出现次数依然小于 k。

根据上面的结论，我们发现：当限定字符种类数目为 t 时，满足题意的最长子串，就一定出自某个 $s[l_{min}...r]$。因此，在滑动窗口的维护过程中，就可以直接得到最长子串的大小。

此外还剩下一个细节：如何判断某个子串内的字符是否都出现了至少 k 次？我们当然可以每次遍历 cnt 数组，但是这会带来O(∣Σ∣) 的额外开销。

我们可以维护一个计数器 less，代表当前出现次数小于 k 的字符的数量。注意到：每次移动滑动窗口的边界时，只会让某个字符的出现次数加一或者减一。对于移动右边界 l 的情况而言：

当某个字符的出现次数从 0 增加到 1 时，将 less 加一；

当某个字符的出现次数从 k−1 增加到 k 时，将 less 减一。

对于移动左边界的情形，讨论是类似的。

通过维护额外的计数器 less，我们无需遍历 cnt 数组，就能知道每个字符是否都出现了至少 k 次，同时可以在每次循环时，在常数时间内更新计数器的取值。读者可以自行思考 k=1时的处理逻辑。

#### 复杂度分析

时间复杂度：$O(N \cdot |\Sigma| + |\Sigma|^2)$，其中 N 为字符串的长度，Σ 为字符集，本题中字符串仅包含小写字母，因此 |∣Σ∣=26。我们需要遍历所有可能的 t，共 ∣Σ∣ 种可能性；内层循环中滑动窗口的复杂度为 O(N)，且初始时需要 O(∣Σ∣) 的时间初始化 cnt 数组。

空间复杂度：O(∣Σ∣)。

