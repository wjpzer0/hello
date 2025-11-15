给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例 1：
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。

示例 2：
输入: "cbbd"
输出: "bb"

#### 我的解法

```python
class Solution(object):
    def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        i = 0
        max_len = 0
        max_s = ""
        while i < len(s):
            j = i + 1
            while j < len(s)+1:
                if s[i:j] == s[i:j][::-1]:
                    if len(s[i:j]) > max_len:
                        max_len = len(s[i:j])
                        max_s = s[i:j]
                j += 1
            i += 1
        return max_s注:该方法为暴力破解，当遇到字符串过长是，耗费时间极长
```

#### 更优的解法

方法一：动态规划

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        dp = [[False] * n for _ in range(n)]
        ans = ""
        # 枚举子串的长度 l+1
        for l in range(n):
            # 枚举子串的起始位置 i，这样可以通过 j=i+l 得到子串的结束位置
            for i in range(n):
                j = i + l
                if j >= len(s):
                   break
                if l == 0:
                    dp[i][j] = True
                elif l == 1:
                    dp[i][j] = (s[i] == s[j])
                else:
                    dp[i][j] = (dp[i + 1][j - 1] and s[i] == s[j])
                if dp[i][j] and l + 1 > len(ans):
                    ans = s[i:j+1]
        return ans
```



复杂度分析
时间复杂度：O(n^2)，其中 nn 是字符串的长度。
动态规划的状态总数为 O(n^2)，对于每个状态，我们需要转移的时间为 O(1)。
空间复杂度：O(n^2)，即存储动态规划状态需要的空间。

方法二：中心扩展算法

```python
class Solution:
    def expandAroundCenter(self, s, left, right):
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return left + 1, right - 1
    def longestPalindrome(self, s: str) -> str:
        start, end = 0, 0
        for i in range(len(s)):
            left1, right1 = self.expandAroundCenter(s, i, i)
            left2, right2 = self.expandAroundCenter(s, i, i + 1)
            if right1 - left1 > end - start:
                start, end = left1, right1
            if right2 - left2 > end - start:
                start, end = left2, right2
        return s[start: end + 1]
```


复杂度分析

时间复杂度：O(n^2)，其中 n 是字符串的长度。长度为 11和 2 的回文中心分别有 n 和 n-1个，每个回文中心最多会向外扩展 O(n) 次。
空间复杂度：O(1)。

方法三：Manacher 算法

```python
class Solution:
    def expand(self, s, left, right):
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return (right - left - 2) // 2
    def longestPalindrome(self, s: str) -> str:
        end, start = -1, 0
        s = '#' + '#'.join(list(s)) + '#'
        arm_len = []
        right = -1
        j = -1
        for i in range(len(s)):
            if right >= i:
                i_sym = 2 * j - i
                min_arm_len = min(arm_len[i_sym], right - i)
                cur_arm_len = self.expand(s, i - min_arm_len, i + min_arm_len)
            else:
                cur_arm_len = self.expand(s, i, i)
            arm_len.append(cur_arm_len)
            if i + cur_arm_len > right:
                j = i
                right = i + cur_arm_len
            if 2 * cur_arm_len + 1 > end - start:
                start = i - cur_arm_len
                end = i + cur_arm_len
        return s[start+1:end+1:2]
```

复杂度分析

时间复杂度：O(n)，其中 nn 是字符串的长度。
由于对于每个位置，扩展要么从当前的最右侧臂长 right 开始，要么只会进行一步，而 right 最多向前走 O(n) 步，因此算法的复杂度为 O(n)。
空间复杂度：O(n)，我们需要 O(n) 的空间记录每个位置的臂长。
