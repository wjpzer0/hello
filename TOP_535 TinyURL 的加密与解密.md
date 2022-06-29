# 题目

TinyURL 是一种 URL 简化服务， 比如：当你输入一个 URL `https://leetcode.com/problems/design-tinyurl` 时，它将返回一个简化的URL `http://tinyurl.com/4e9iAk` 。请你设计一个类来加密与解密 TinyURL 。

加密和解密算法如何设计和运作是没有限制的，你只需要保证一个 URL 可以被加密成一个 TinyURL ，并且这个 TinyURL 可以用解密方法恢复成原本的 URL 。

实现 `Solution` 类：

- `Solution()` 初始化 TinyURL 系统对象。
- `String encode(String longUrl)` 返回 `longUrl` 对应的 TinyURL 。
- `String decode(String shortUrl)` 返回 `shortUrl` 原本的 URL 。题目数据保证给定的 `shortUrl` 是由同一个系统对象加密的。

**示例：**

```
输入：url = "https://leetcode.com/problems/design-tinyurl"
输出："https://leetcode.com/problems/design-tinyurl"

解释：
Solution obj = new Solution();
string tiny = obj.encode(url); // 返回加密后得到的 TinyURL 。
string ans = obj.decode(tiny); // 返回解密后得到的原本的 URL 。
```

**提示：**

- $1 <= url.length <= 10^4$
- 题目数据保证 `url` 是一个有效的 URL

## 我的解法

未解出！把题目想复杂了！以为要压缩字符串！

### 前言

题目不要求相同的 $\text{URL}$必须加密成同一个 $\text{TinyURL}$，因此本文的方法不满足相同的 $\text{URL}$加密成同一个 $\text{TinyURL}$。如果想要实现相同的$ \text{URL}$加密成同一个 $\text{TinyURL}$，则额外保存一个从 $\text{URL}$到 \text{TinyURL}的映射。

## 其他解法

### 自增

```c++
class Solution {
private:
    unordered_map<int, string> dataBase;
    int id;

public:
    Solution() {
        id = 0;
    }

    string encode(string longUrl) {
        id++;
        dataBase[id] = longUrl;
        return string("http://tinyurl.com/") + to_string(id);
    }

    string decode(string shortUrl) {
        int p = shortUrl.rfind('/') + 1;
        int key = stoi(shortUrl.substr(p, int(shortUrl.size()) - p));
        return dataBase[key];
    }
};
```

```java
public class Codec {
    private Map<Integer, String> dataBase = new HashMap<Integer, String>();
    private int id;

    public String encode(String longUrl) {
        id++;
        dataBase.put(id, longUrl);
        return "http://tinyurl.com/" + id;
    }

    public String decode(String shortUrl) {
        int p = shortUrl.lastIndexOf('/') + 1;
        int key = Integer.parseInt(shortUrl.substring(p));
        return dataBase.get(key);
    }
}
```

```python
class Codec:
    def __init__(self):
        self.database = {}
        self.id = 0

    def encode(self, longUrl: str) -> str:
        self.id += 1
        self.database[self.id] = longUrl
        return "http://tinyurl.com/" + str(self.id)

    def decode(self, shortUrl: str) -> str:
        i = shortUrl.rfind('/')
        id = int(shortUrl[i + 1:])
        return self.database[id]
```

$\text{Encode}$函数

使用自增 $\textit{id}$作为 $\textit{longUrl}$的键，每接收一个 $\textit{longUrl}$都将 $\textit{id}$加一，将键值对 $(\textit{id}, \textit{longUrl})$ 插入数据库 $\textit{dataBase}$，然后返回带有 $\textit{id}$的字符串作为$ \textit{shorUrl}$。

$\text{Decode}$函数

将 $\textit{shortUrl}$转换成对应的 $\textit{key}$，然后在数据库 $\textit{dataBase}$中查找$ \textit{key}$对应的 $\textit{longUrl}$。

#### 复杂度分析

- 时间复杂度：

  - $\text{Encode}$函数：O(n)，其中 n 是字符串 $\textit{longUrl}$的长度。

  - $\text{Decode}$函数：O(1)。我们当 $\textit{shortUrl}$当成有限长度的字符串看待。

- 空间复杂度：

  - $\text{Encode}$函数：O(n)。保存字符串 $\textit{longUrl}$需要 O(n) 的空间。

  - $\text{Decode}$函数：O(1)。

### 哈希生成

```c++
const long long k1 = 1117;
const long long k2 = 1e9 + 7;

class Solution {
private:
    unordered_map<int, string> dataBase;
    unordered_map<string, int> urlToKey;

public:
    Solution() {

    }

    string encode(string longUrl) {
        if (urlToKey.count(longUrl) > 0) {
            return string("http://tinyurl.com/") + to_string(urlToKey[longUrl]);
        }
        long long key = 0, base = 1;
        for (auto c : longUrl) {
            key = (key + c * base) % k2;
            base = (base * k1) % k2;
        }
        while (dataBase.count(key) > 0) {
            key = (key + 1) % k2;
        }
        dataBase[key] = longUrl;
        urlToKey[longUrl] = key;
        return string("http://tinyurl.com/") + to_string(key);
    }

    string decode(string shortUrl) {
        int p = shortUrl.rfind('/') + 1;
        int key = stoi(shortUrl.substr(p, int(shortUrl.size()) - p));
        return dataBase[key];
    }
};
```

```java
public class Codec {
    static final int K1 = 1117;
    static final int K2 = 1000000007;
    private Map<Integer, String> dataBase = new HashMap<Integer, String>();
    private Map<String, Integer> urlToKey = new HashMap<String, Integer>();

    public String encode(String longUrl) {
        if (urlToKey.containsKey(longUrl)) {
            return "http://tinyurl.com/" + urlToKey.get(longUrl);
        }
        int key = 0;
        long base = 1;
        for (int i = 0; i < longUrl.length(); i++) {
            char c = longUrl.charAt(i);
            key = (int) ((key + (long) c * base) % K2);
            base = (base * K1) % K2;
        }
        while (dataBase.containsKey(key)) {
            key = (key + 1) % K2;
        }
        dataBase.put(key, longUrl);
        urlToKey.put(longUrl, key);
        return "http://tinyurl.com/" + key;
    }

    public String decode(String shortUrl) {
        int p = shortUrl.lastIndexOf('/') + 1;
        int key = Integer.parseInt(shortUrl.substring(p));
        return dataBase.get(key);
    }
}
```

```python
K1, K2 = 1117, 10 ** 9 + 7

class Codec:
    def __init__(self):
        self.dataBase = {}
        self.urlToKey = {}

    def encode(self, longUrl: str) -> str:
        if longUrl in self.urlToKey:
            return "http://tinyurl.com/" + str(self.urlToKey[longUrl])
        key, base = 0, 1
        for c in longUrl:
            key = (key + ord(c) * base) % K2
            base = (base * K1) % K2
        while key in self.dataBase:
            key = (key + 1) % K2
        self.dataBase[key] = longUrl
        self.urlToKey[longUrl] = key
        return "http://tinyurl.com/" + str(key)

    def decode(self, shortUrl: str) -> str:
        i = shortUrl.rfind('/')
        key = int(shortUrl[i + 1:])
        return self.dataBase[key]
```



- $\text{Encode}$函数

  设字符串 $\textit{longUrl}$的长度为 n，选择两个合适的质数 $k_1 = 1117$，$k_2 = 10^9 + 7$，使用以下方法来计算 $\textit{longUrl}$的哈希值：

  $$
  \text{Hash} ( \textit{longUrl} ) = \big ( \sum^{n-1}_{i=0} \textit{longUrl}[i] \times k_1^i \big ) \bmod k_2
  $$

  将哈希值作为 $\textit{longUrl}$的 $\textit{key}$，将键值对 $(\textit{key}, \textit{longUrl})$ 插入数据库 $\textit{dataBase}$，然后返回带有 $\textit{key}$的字符串作为 $\textit{shorUrl}$。

  > 发生哈希冲突时，我们采用线性探测再散列的方法，将 $\textit{key}$加一，直到没有冲突。相同的 $\textit{longUrl}$的哈希值相同，因此哈希冲突会频繁发生。为了避免这一点，我们使用一个额外的哈希表记录从 $\textit{longUrl}$到 $\textit{key}$的映射。

- $\text{Decode}$函数

- 将 $\textit{shortUrl}$转换成对应的 $\textit{key}$，然后在数据库 $\textit{dataBase}$中查找 $\textit{key}$对应的 $\textit{longUrl}$。

#### 复杂度分析

- 时间复杂度：

  - $\text{Encode}$函数：O(n)，其中 n 是字符串 $\textit{longUrl}$的长度。在数据量远小于 $10^9 + 7$ 的情况下，发生哈希冲突的可能性十分小。
  - $\text{Decode}$函数：O(1)。我们当 $\textit{shortUrl}$当成有限长度的字符串看待。

- 空间复杂度：

  - $\text{Encode}$函数：O(n)。保存字符串 $\textit{longUrl}$需要 O(n) 的空间。

  - $\text{Decode}$函数：O(1)。


### 随机生成

```c++
class Solution {
private:
    unordered_map<int, string> dataBase;

public:
    Solution() {
        srand(time(0));
    }

    string encode(string longUrl) {
        int key;
        while (true) {
            key = rand();
            if (dataBase.count(key) == 0) {
                break;
            }
        }
        dataBase[key] = longUrl;
        return string("http://tinyurl.com/") + to_string(key);
    }

    string decode(string shortUrl) {
        int p = shortUrl.rfind('/') + 1;
        int key = stoi(shortUrl.substr(p, int(shortUrl.size()) - p));
        return dataBase[key];
    }
};
```

```java
public class Codec {
    private Map<Integer, String> dataBase = new HashMap<Integer, String>();
    private Random random = new Random();

    public String encode(String longUrl) {
        int key;
        while (true) {
            key = random.nextInt();
            if (!dataBase.containsKey(key)) {
                break;
            }
        }
        dataBase.put(key, longUrl);
        return "http://tinyurl.com/" + key;
    }

    public String decode(String shortUrl) {
        int p = shortUrl.lastIndexOf('/') + 1;
        int key = Integer.parseInt(shortUrl.substring(p));
        return dataBase.get(key);
    }
}
```

```python
class Codec:
    def __init__(self):
        self.dataBase = {}

    def encode(self, longUrl: str) -> str:
        while True:
            key = randrange(maxsize)
            if key not in self.dataBase:
                self.dataBase[key] = longUrl
                return "http://tinyurl.com/" + str(key)

    def decode(self, shortUrl: str) -> str:
        i = shortUrl.rfind('/')
        key = int(shortUrl[i + 1:])
        return self.dataBase[key]
```

- $\text{Encode}$函数

  使用一个随机生成的整数作为 $\textit{longUrl}$的 $\textit{key}$，如果 $\textit{key}$已经重复，那么不断尝试随机生成整数，直到 $\textit{key}$唯一。将键值对 $(\textit{key}, \textit{longUrl}) $插入数据库 $\textit{dataBase}$，然后返回带有 $\textit{key}$的字符串作为 $\textit{shorUrl}$。

- $\text{Decode}$函数

  将 $\textit{shortUrl}$转换成对应的 $\textit{key}$，然后在数据库 $\textit{dataBase}$中查找 $\textit{key}$对应的 $\textit{longUrl}$。


#### 复杂度分析

- 时间复杂度：

  - $\text{Encode}$函数：O(n)，其中 n 是字符串 $\textit{longUrl}$的长度。在数据量远小于 $2^{32}$ 的情况下，随机生成的整数重复的可能性十分小。
  - $\text{Decode}$函数：O(1)。我们当 $\textit{shortUrl}$当成有限长度的字符串看待。

- 空间复杂度：

  - $\text{Encode} $函数：O(n)。保存字符串 $\textit{longUrl}$需要 O(n)O(n) 的空间。

  - $\text{Decode}$函数：O(1)。


