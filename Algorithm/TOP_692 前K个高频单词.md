# 题目

给一非空的单词列表，返回前 k 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。

示例 1：

```
输入: ["i", "love", "leetcode", "i", "love", "coding"], k = 2
输出: ["i", "love"]
解析: "i" 和 "love" 为出现次数最多的两个单词，均为2次。
    注意，按字母顺序 "i" 在 "love" 之前。
```


示例 2：

```
输入: ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"], k = 4
输出: ["the", "is", "sunny", "day"]
解析: "the", "is", "sunny" 和 "day" 是出现次数最多的四个单词，
    出现次数依次为 4, 3, 2 和 1 次。
```


注意：

1. 假定 k 总为有效值， 1 ≤ k ≤ 集合元素数。
2. 输入的单词均由小写字母组成。


扩展练习：

尝试以 O(n log k) 时间复杂度和 O(n) 空间复杂度解决。

## 我的解法

未解出，对多层排序无法很好的应用！

### 使用「哈希表」&「优先队列」

- 使用「哈希表」来统计所有的词频

- 构建大小为 k 按照「词频升序 + (词频相同)字典序倒序」的优先队列：
  - 如果词频不相等，根据词频进行升序构建，确保堆顶元素是堆中词频最小的元素
  - 如果词频相等，根据字典序大小进行倒序构建，结合 2.1 可以确保堆顶元素是堆中「词频最小 & 字典序最大」的元素
  
- 对所有元素进行遍历，尝试入堆：
  - 堆内元素不足 k 个：直接入堆
  - 词频大于堆顶元素：堆顶元素不可能是前 k 大的元素。将堆顶元素弹出，并将当前元素添加到堆中
  - 词频小于堆顶元素；当前元素不可能是前 k 大的元素，直接丢弃。
  - 词频等于堆顶元素：根据当前元素与堆顶元素的字典序大小决定（如果字典序大小比堆顶元素要小则入堆）
  
- 输出堆内元素，并翻转

  ```java
  class Solution {
      public List<String> topKFrequent(String[] ws, int k) {
          Map<String, Integer> map = new HashMap<>();
          for (String w : ws) map.put(w, map.getOrDefault(w, 0) + 1);
          PriorityQueue<Object[]> q = new PriorityQueue<>(k, (a, b)->{ 
              // 如果词频不同，根据词频升序
              int c1 = (Integer)a[0], c2 = (Integer)b[0];
              if (c1 != c2) return c1 - c2;
              // 如果词频相同，根据字典序倒序
              String s1 = (String)a[1], s2 = (String)b[1];
              return s2.compareTo(s1);
          });
          for (String s : map.keySet()) {
              int cnt = map.get(s);
              if (q.size() < k) { // 不足 k 个，直接入堆
                  q.add(new Object[]{cnt, s});
              } else {
                  Object[] peek = q.peek();
                  if (cnt > (Integer)peek[0]) { // 词频比堆顶元素大，弹出堆顶元素，入堆
                      q.poll();
                      q.add(new Object[]{cnt, s});
                  } else if (cnt == (Integer)peek[0]) { // 词频与堆顶元素相同
                      String top = (String)peek[1];
                      if (s.compareTo(top) < 0) { // 且字典序大小比堆顶元素小，弹出堆顶元素，入堆
                          q.poll();
                          q.add(new Object[]{cnt, s});
                      }
                  }
              }
          }
          List<String> ans = new ArrayList<>();
          while (!q.isEmpty()) ans.add((String)q.poll()[1]);
          Collections.reverse(ans);
          return ans;
      }
  }
  ```

## 其他解法

### 哈希表 + 排序

```c++
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> cnt;
        for (auto& word : words) {
            ++cnt[word];
        }
        vector<string> rec;
        for (auto& [key, value] : cnt) {
            rec.emplace_back(key);
        }
        sort(rec.begin(), rec.end(), [&](const string& a, const string& b) -> bool {
            return cnt[a] == cnt[b] ? a < b : cnt[a] > cnt[b];
        });
        rec.erase(rec.begin() + k, rec.end());
        return rec;
    }
};
```

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> cnt = new HashMap<String, Integer>();
        for (String word : words) {
            cnt.put(word, cnt.getOrDefault(word, 0) + 1);
        }
        List<String> rec = new ArrayList<String>();
        for (Map.Entry<String, Integer> entry : cnt.entrySet()) {
            rec.add(entry.getKey());
        }
        Collections.sort(rec, new Comparator<String>() {
            public int compare(String word1, String word2) {
                return cnt.get(word1) == cnt.get(word2) ? word1.compareTo(word2) : cnt.get(word2) - cnt.get(word1);
            }
        });
        return rec.subList(0, k);
    }
}
```

```python
class Solution:
    def topKFrequent(self, words: List[str], k: int) -> List[str]:
        dic = {} # 统计每一个单词出现的频率
        for i in words:
            if i not in dic.keys():
                dic[i] = 1
            else:
                dic[i] += 1
        # 排序, 先按照词频降序, 再按照字母顺序升序
        # 这里比价关键的是sorted函数
        # dic.items()将字典dic变成了可迭代对象，迭代key和对应的value
        # 若sorted函数的参数key = lambda item: item[0] 表示按照字典的key进行排序
        # 若sorted函数的参数key = lambda item: item[1] 表示按照字典的value进行排序
        # key = lambda item: (-item[1],item[0])表示排序的依据是元组(-value,key),即先按照频数的倒数排序(即保证了频数降序排列)，然后对于相同频数的单词再比价大小（题中说按照字母顺序，就正好对应字母升序）
        dic = sorted(dic.items(), key = lambda item: (-item[1],item[0]))
        return [key for key,_ in dic[:k]] # 将前k个高频单词输出即可
```

#### 思路及算法

我们可以预处理出每一个单词出现的频率，然后依据每个单词出现的频率降序排序，最后返回前 k 个字符串即可。

具体地，我们利用哈希表记录每一个字符串出现的频率，然后将哈希表中所有字符串进行排序，排序时，如果两个字符串出现频率相同，那么我们让两字符串中字典序较小的排在前面，否则我们让出现频率较高的排在前面。最后我们只需要保留序列中的前 kk 个字符串即可。

#### 复杂度分析

- 时间复杂度：O(l×n+l×mlogm)，其中 n 表示给定字符串序列的长度，l 表示字符串的平均长度，m 表示实际字符串种类数。我们需要 l×n 的时间将字符串插入到哈希表中，以及 l×mlogm 的时间完成字符串比较（最坏情况下所有字符串出现频率都相同，我们需要将它们两两比较）。

- 空间复杂度：O(l×m)，其中 ll 表示字符串的平均长度，mm 表示实际字符串种类数。哈希表和生成的排序数组空间占用均为 O(l×m)。


### 优先队列

```c++
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> cnt;
        for (auto& word : words) {
            cnt[word]++;
        }
        auto cmp = [](const pair<string, int>& a, const pair<string, int>& b) {
            return a.second == b.second ? a.first < b.first : a.second > b.second;
        };
        priority_queue<pair<string, int>, vector<pair<string, int>>, decltype(cmp)> que(cmp);
        for (auto& it : cnt) {
            que.emplace(it);
            if (que.size() > k) {
                que.pop();
            }
        }
        vector<string> ret(k);
        for (int i = k - 1; i >= 0; i--) {
            ret[i] = que.top().first;
            que.pop();
        }
        return ret;
    }
};
```

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> cnt = new HashMap<String, Integer>();
        for (String word : words) {
            cnt.put(word, cnt.getOrDefault(word, 0) + 1);
        }
        PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<Map.Entry<String, Integer>>(new Comparator<Map.Entry<String, Integer>>() {
            public int compare(Map.Entry<String, Integer> entry1, Map.Entry<String, Integer> entry2) {
                return entry1.getValue() == entry2.getValue() ? entry2.getKey().compareTo(entry1.getKey()) : entry1.getValue() - entry2.getValue();
            }
        });
        for (Map.Entry<String, Integer> entry : cnt.entrySet()) {
            pq.offer(entry);
            if (pq.size() > k) {
                pq.poll();
            }
        }
        List<String> ret = new ArrayList<String>();
        while (!pq.isEmpty()) {
            ret.add(pq.poll().getKey());
        }
        Collections.reverse(ret);
        return ret;
    }
}
```

#### 思路及算法

对于前 kk 大或前 k 小这类问题，有一个通用的解法：优先队列。优先队列可以在 O(logn) 的时间内完成插入或删除元素的操作（其中 n 为优先队列的大小），并可以 O(1) 地查询优先队列顶端元素。

在本题中，我们可以创建一个小根优先队列（顾名思义，就是优先队列顶端元素是最小元素的优先队列）。我们将每一个字符串插入到优先队列中，如果优先队列的大小超过了 kk，那么我们就将优先队列顶端元素弹出。这样最终优先队列中剩下的 kk 个元素就是前 kk 个出现次数最多的单词。

#### 复杂度分析

- 时间复杂度：O(l×n+m×llogk)，其中 nn 表示给定字符串序列的长度，m 表示实际字符串种类数，l 表示字符串的平均长度。我们需要 l×n 的时间将字符串插入到哈希表中，以及每次插入元素到优先队列中都需要 llogk 的时间，共需要插入 m 次。

- 空间复杂度：O(l×(m+k))，其中 l 表示字符串的平均长度，m 表示实际字符串种类数。哈希表空间占用为 O(l×m)，优先队列空间占用为 O(l×k)。

