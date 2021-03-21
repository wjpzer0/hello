给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

示例 2:
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

示例 3:
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

#### 我的解法

未解出

#### 更优解法

我们先用一个例子来想一想如何在较优的时间复杂度内通过本题。
我们不妨以示例一中的字符串  {abcabcbb}abcabcbb 为例，找出 从每一个字符开始的，
不包含重复字符的最长子串，那么其中最长的那个字符串即为答案。
对于示例一中的字符串，我们列举出这些结果，其中括号中表示选中的字符以及最长的字符串：
以 {(a)bcabcbb}(a)bcabcbb 开始的最长字符串为  {(abc)abcbb}(abc)abcbb；
以 {a(b)cabcbb}a(b)cabcbb 开始的最长字符串为  {a(bca)bcbb}a(bca)bcbb；
以 {ab(c)abcbb}ab(c)abcbb 开始的最长字符串为  {ab(cab)cbb}ab(cab)cbb；
以 {abc(a)bcbb}abc(a)bcbb 开始的最长字符串为  {abc(abc)bb}abc(abc)bb；
以 {abca(b)cbb}abca(b)cbb 开始的最长字符串为  {abca(bc)bb}abca(bc)bb；
以 {abcab(c)bb}abcab(c)bb 开始的最长字符串为  {abcab(cb)b}abcab(cb)b；
以 {abcabc(b)b}abcabc(b)b 开始的最长字符串为  {abcabc(b)b}abcabc(b)b；`
以 {abcabcb(b)}abcabcb(b) 开始的最长字符串为  {abcabcb(b)}abcabcb(b)。
发现了什么？如果我们依次递增地枚举子串的起始位置，那么子串的结束位置也是递增的！
这里的原因在于，假设我们选择字符串中的第 k个字符作为起始位置，并且得到了不包含重复字符的最长子串的结束位置为 r_k。
那么当我们选择第 k+1个字符作为起始位置时，首先从 k+1 到 r_k 
k的字符显然是不重复的，并且由于少了原本的第 k个字符，我们可以尝试继续增大 r_k，直到右侧出现了重复字符为止。

这样以来，我们就可以使用「滑动窗口」来解决这个问题了：

我们使用两个指针表示字符串中的某个子串（的左右边界）。
其中左指针代表着上文中「枚举子串的起始位置」，而右指针即为上文中的 r_k；
在每一步的操作中，我们会将左指针向右移动一格，表示 我们开始枚举下一个字符作为起始位置，
然后我们可以不断地向右移动右指针，但需要保证这两个指针对应的子串中没有重复的字符。
在移动结束后，这个子串就对应着 以左指针开始的，不包含重复字符的最长子串。我们记录下这个子串的长度；
在枚举结束后，我们找到的最长的子串的长度即为答案

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        # 哈希集合，记录每个字符是否出现过
        occ = set()
        n = len(s)
        # 右指针，初始值为 -1，相当于我们在字符串的左边界的左侧，还没有开始移动
        rk, ans = -1, 0
        for i in range(n):
            if i != 0:
                # 左指针向右移动一格，移除一个字符
                occ.remove(s[i - 1])
            while rk + 1 < n and s[rk + 1] not in occ:
                # 不断地移动右指针
                occ.add(s[rk + 1])
                rk += 1
            # 第 i 到 rk 个字符是一个极长的无重复字符子串
            ans = max(ans, rk - i + 1)
        return ans
```


复杂度分析

时间复杂度：O(N)，其中 N 是字符串的长度。左指针和右指针分别会遍历整个字符串一次。
空间复杂度：O(∣Σ∣)，其中 Σ 表示字符集（即字符串中可以出现的字符），∣Σ∣ 表示字符集的大小。
在本题中没有明确说明字符集，因此可以默认为所有 ASCII 码在 [0, 128)[0,128) 内的字符，即 ∣Σ∣=128。
我们需要用到哈希集合来存储出现过的字符，而字符最多有 ∣Σ∣ 个，因此空间复杂度为 O(∣Σ∣)。

使用defaultdict任何未定义的key都会默认返回一个根据method_factory参数不同的默认值, 而相同情况下dict()会返回KeyError.
比较下面代码:
d1 = dict()
d2 = defaultdict(list)
print(d1['a'])
print(d2['a'])

defaultdict的构造
python官方文档中对defaultdict的定义如下:
class collections.defaultdict([default_factory[, ...]])

python官方文档中对defaultdict的解释如下:
defaultdic
dict subclass that calls a factory function to supply missing values
default_factory 接收一个工厂函数作为参数, 例如int str list set等.
defaultdict在dict的基础上添加了一个__missing__(key)方法, 在调用一个不存的key的时候, defaultdict会调用__missing__, 返回一个根据default_factory参数的默认值, 所以不会返回Keyerror.

Example 1
s = 'mississippi'
d = defaultdict(int)
for k in s:
    d[k] += 1
print(d)
defaultdict(<class 'int'>, {'m': 1, 'i': 4, 's': 4, 'p': 2})

Example 2
s = [('red', 1), ('blue', 2), ('red', 3), ('blue', 4), ('red', 1), ('blue', 4)]
d = defaultdict(set)
for k, v in s:
    d[k].add(v)
print(d)
defaultdict(<class 'set'>, {'red': {1, 3}, 'blue': {2, 4}})

Python set() 函数
set() 函数创建一个无序不重复元素集，可进行关系测试，删除重复数据，还可以计算交集、差集、并集等。
set 语法：
class set([iterable])

参数说明：
iterable -- 可迭代对象对象；

返回值
返回新的集合对象。

实例
>>>x = set('runoob')
>>> y = set('google')
>>> x, y
(set(['b', 'r', 'u', 'o', 'n']), set(['e', 'o', 'g', 'l']))   # 重复的被删除
>>> x & y         # 交集
set(['o'])
>>> x | y         # 并集
set(['b', 'e', 'g', 'l', 'o', 'n', 'r', 'u'])
>>> x - y         # 差集
set(['r', 'b', 'u', 'n'])