# 题目

给出第一个词 `first` 和第二个词 `second`，考虑在某些文本 `text` 中可能以 `"first second third"` 形式出现的情况，其中 `second` 紧随 `first` 出现，`third` 紧随 `second` 出现。

对于每种这样的情况，将第三个词 "`third`" 添加到答案中，并返回答案。

**示例 1：**

```
输入：text = "alice is a good girl she is a good student", first = "a", second = "good"
输出：["girl","student"]
```

**示例 2：**

```
输入：text = "we will we will rock you", first = "we", second = "will"
输出：["we","rock"]
```

**提示：**

- `1 <= text.length <= 1000`
- `text` 由小写英文字母和空格组成
- `text` 中的所有单词之间都由 **单个空格字符** 分隔
- `1 <= first.length, second.length <= 10`
- `first` 和 `second` 由小写英文字母组成

## 我的解法

没啥问题！

```python
class Solution:
    def findOcurrences(self, text: str, first: str, second: str) -> List[str]:
        ans = []
        newText = text.split()
        n = len(newText)
        for i in range(n - 2):
            if newText[i] == first and newText[i + 1] == second:
                ans.append(newText[i + 2])
        return ans
```

## 其他解法

### 遍历

```c
char ** findOcurrences(char * text, char * first, char * second, int* returnSize){
    int s = 0, e = 0, len = strlen(text);
    char **words = (char **)malloc(sizeof(char *) * len);
    memset(words, 0, sizeof(char *) * len);
    int nwords = 0;
    while (true) {
        while (s < len && text[s] == ' ') {
            s++;
        }
        if (s >= len) {
            break;
        }
        e = s + 1;
        while (e < len && text[e] != ' ') {
            e++;
        }
        words[nwords++] = strndup(text + s, e - s);
        s = e + 1;
    }
    char **ret = (char **)malloc(sizeof(char *) * (nwords + 1));
    memset(ret, 0, sizeof(char *) * (nwords + 1));
    int nret = 0;
    for (int i = 2; i < nwords; i++) {
        if (strcmp(words[i - 2], first) == 0 && strcmp(words[i - 1], second) == 0) {
            ret[nret++] = strdup(words[i]);
        }
    }
    for (int i = 0; i < nwords; i++) {
        free(words[i]);
    }
    free(words);
    *returnSize = nret;
    return ret;
}
```

```c++
class Solution {
public:
    vector<string> findOcurrences(string text, string first, string second) {
        vector<string> words;
        int s = 0, e = 0, len = text.length();
        while (true) {
            while (s < len && text[s] == ' ') {
                s++;
            }
            if (s >= len) {
                break;
            }
            e = s + 1;
            while (e < len && text[e] != ' ') {
                e++;
            }
            words.push_back(text.substr(s, e - s));
            s = e + 1;
        }
        vector<string> ret;
        for (int i = 2; i < words.size(); i++) {
            if (words[i - 2] == first && words[i - 1] == second) {
                ret.push_back(words[i]);
            }
        }
        return ret;
    }
};
```

```java
class Solution {
    public String[] findOcurrences(String text, String first, String second) {
        String[] words = text.split(" ");
        List<String> list = new ArrayList<String>();
        for (int i = 2; i < words.length; i++) {
            if (words[i - 2].equals(first) && words[i - 1].equals(second)) {
                list.add(words[i]);
            }
        }
        int size = list.size();
        String[] ret = new String[size];
        for (int i = 0; i < size; i++) {
            ret[i] = list.get(i);
        }
        return ret;
    }
}
```

```python
class Solution:
    def findOcurrences(self, text: str, first: str, second: str) -> List[str]:
        words = text.split()
        return [words[i] for i in range(2, len(words)) if words[i - 2] == first and words[i - 1] == second]
```

#### 思路与算法

我们将文本$ \textit{text}$ 按空格分割成单词数组 $\textit{words}$，然后遍历 $\textit{words}$ 数组，如果一个单词的前两个单词分别按顺序等于 $\textit{first}$ 和 $\textit{second}$，则该单词符合第三个单词 $\textit{third}$ 的定义, 将其加入结果中。

#### 复杂度分析

- 时间复杂度：O(N)，其中 N 为$ \textit{text}$text 的长度。分割 $\textit{text}$text 需要 O(N)O(N)，$\textit{words}$ 每个元素最多访问两次，需要 O(N)，所以总的时间复杂度为 O(N)。

- 空间复杂度：O(N)。需要 O(N) 的空间来保存 $\textit{words}$ 数组。


