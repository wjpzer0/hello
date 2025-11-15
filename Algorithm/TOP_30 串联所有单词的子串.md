# 题目

给定一个字符串 `s` 和一些 **长度相同** 的单词 `words` **。**找出 `s` 中恰好可以由 `words` 中所有单词串联形成的子串的起始位置。

注意子串要与 `words` 中的单词完全匹配，**中间不能有其他字符** ，但不需要考虑 `words` 中单词串联的顺序。

**示例 1：**

```
输入：s = "barfoothefoobarman", words = ["foo","bar"]
输出：[0,9]
解释：
从索引 0 和 9 开始的子串分别是 "barfoo" 和 "foobar" 。
输出的顺序不重要, [9,0] 也是有效答案。
```

**示例 2：**

```
输入：s = "wordgoodgoodgoodbestword", words = ["word","good","best","word"]
输出：[]
```

**示例 3：**

```
输入：s = "barfoofoobarthefoobarman", words = ["bar","foo","the"]
输出：[6,9,12]
```

**提示：**

- $1 <= s.length <= 10^4$
- `s` 由小写英文字母组成
- `1 <= words.length <= 5000`
- `1 <= words[i].length <= 30`
- `words[i]` 由小写英文字母组成

## 我的解法

想到了滑动窗口，想到了字典对比，但是移动的格数弄错了，查看大佬题解后写出！

```python
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        cnt, l = len(words), len(words[0])
        i, j = 0, cnt * l 
        ans = []
        check = collections.Counter(words)
        while j <= len(s):
            ss = s[i : j]
            tmp = []
            for k in range(0, len(ss), l):
                tmp.append(ss[k: k + l])
            if collections.Counter(tmp) == check:
                ans.append(i)
            i += 1
            j += 1
        return ans
```

## 其他解法

### 滑动窗口

```c++
class Solution {
public:
    vector<int> findSubstring(string &s, vector<string> &words) {
        vector<int> res;
        int m = words.size(), n = words[0].size(), ls = s.size();
        for (int i = 0; i < n && i + m * n <= ls; ++i) {
            unordered_map<string, int> differ;
            for (int j = 0; j < m; ++j) {
                ++differ[s.substr(i + j * n, n)];
            }
            for (string &word: words) {
                if (--differ[word] == 0) {
                    differ.erase(word);
                }
            }
            for (int start = i; start < ls - m * n + 1; start += n) {
                if (start != i) {
                    string word = s.substr(start + (m - 1) * n, n);
                    if (++differ[word] == 0) {
                        differ.erase(word);
                    }
                    word = s.substr(start - n, n);
                    if (--differ[word] == 0) {
                        differ.erase(word);
                    }
                }
                if (differ.empty()) {
                    res.emplace_back(start);
                }
            }
        }
        return res;
    }
};
```

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<Integer>();
        int m = words.length, n = words[0].length(), ls = s.length();
        for (int i = 0; i < n; i++) {
            if (i + m * n > ls) {
                break;
            }
            Map<String, Integer> differ = new HashMap<String, Integer>();
            for (int j = 0; j < m; j++) {
                String word = s.substring(i + j * n, i + (j + 1) * n);
                differ.put(word, differ.getOrDefault(word, 0) + 1);
            }
            for (String word : words) {
                differ.put(word, differ.getOrDefault(word, 0) - 1);
                if (differ.get(word) == 0) {
                    differ.remove(word);
                }
            }
            for (int start = i; start < ls - m * n + 1; start += n) {
                if (start != i) {
                    String word = s.substring(start + (m - 1) * n, start + m * n);
                    differ.put(word, differ.getOrDefault(word, 0) + 1);
                    if (differ.get(word) == 0) {
                        differ.remove(word);
                    }
                    word = s.substring(start - n, start);
                    differ.put(word, differ.getOrDefault(word, 0) - 1);
                    if (differ.get(word) == 0) {
                        differ.remove(word);
                    }
                }
                if (differ.isEmpty()) {
                    res.add(start);
                }
            }
        }
        return res;
    }
}
```

```python
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        res = []
        m, n, ls = len(words), len(words[0]), len(s)
        for i in range(n):
            if i + m * n > ls:
                break
            differ = Counter()
            for j in range(m):
                word = s[i + j * n: i + (j + 1) * n]
                differ[word] += 1
            for word in words:
                differ[word] -= 1
                if differ[word] == 0:
                    del differ[word]
            for start in range(i, ls - m * n + 1, n):
                if start != i:
                    word = s[start + (m - 1) * n: start + m * n]
                    differ[word] += 1
                    if differ[word] == 0:
                        del differ[word]
                    word = s[start - n: start]
                    differ[word] -= 1
                    if differ[word] == 0:
                        del differ[word]
                if len(differ) == 0:
                    res.append(start)
        return res
```

#### 思路

此题是 TOP_438. 找到字符串中所有字母异位词 的进阶版。不同的是第 438 题的元素是字母，而此题的元素是单词。可以用类似 TOP_438. 找到字符串中所有字母异位词 的方法二的滑动窗口来解这题。

记 $\textit{words}$的长度为 m，$\textit{words}$中每个单词的长度为 n，s 的长度为 $\textit{ls}$。首先需要将 s 划分为单词组，每个单词的大小均为 n （首尾除外）。这样的划分方法有 n 种，即先删去前 i （$i = 0 \sim n-1$）个字母后，将剩下的字母进行划分，如果末尾有不到 n 个字母也删去。对这 n 种划分得到的单词数组分别使用滑动窗口对 $\textit{words}$进行类似于「字母异位词」的搜寻。

划分成单词组后，一个窗口包含 s 中前 m 个单词，用一个哈希表 $\textit{differ}$表示窗口中单词频次和 $\textit{words}$中单词频次之差。初始化 $\textit{differ}$时，出现在窗口中的单词，每出现一次，相应的值增加 1，出现在 $\textit{words}$中的单词，每出现一次，相应的值减少 1。然后将窗口右移，右侧会加入一个单词，左侧会移出一个单词，并对 \textit{differ}做相应的更新。窗口移动时，若出现 $\textit{differ}$中值不为 0 的键的数量为 0，则表示这个窗口中的单词频次和 $\textit{words}$中单词频次相同，窗口的左端点是一个待求的起始位置。划分的方法有 n 种，做 n 次滑动窗口后，即可找到所有的起始位置。

#### 复杂度分析

- 时间复杂度：$O(\textit{ls} \times n)$，其中$ \textit{ls}$是输入 s 的长度，n 是 $\textit{words}$中每个单词的长度。需要做 n 次滑动窗口，每次需要遍历一次 s。

- 空间复杂度：$O(m \times n)$，其中 m 是 $\textit{words}$的单词数，n 是 $\textit{words}$中每个单词的长度。每次滑动窗口时，需要用一个哈希表保存单词频次。


