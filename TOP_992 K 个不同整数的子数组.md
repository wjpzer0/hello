# 题目

给定一个正整数数组 A，如果 A 的某个子数组中不同整数的个数恰好为 K，则称 A 的这个连续、不一定独立的子数组为好子数组。

（例如，[1,2,3,1,2] 中有 3 个不同的整数：1，2，以及 3。）

返回 A 中好子数组的数目。

示例 1：

```
输入：A = [1,2,1,2,3], K = 2
输出：7
解释：恰好由 2 个不同整数组成的子数组：[1,2], [2,1], [1,2], [2,3], [1,2,1], [2,1,2], [1,2,1,2].
```


示例 2：

```
输入：A = [1,2,1,3,4], K = 3
输出：3
解释：恰好由 3 个不同整数组成的子数组：[1,2,1,3], [2,1,3], [1,3,4].
```


提示：

1 <= A.length <= 20000
1 <= A[i] <= A.length
1 <= K <= A.length

## 我的解法

```python
class Solution:
    def subarraysWithKDistinct(self, A: List[int], K: int) -> int:
        returnNum = 0
        for left in range(len(A)):
            subarrayA = []
            numK = []
            right = left 
            while right < len(A):
                if A[right] not in numK:
                    numK.append(A[right])
                    if len(numK) < K:
                        subarrayA.append(A[right])
                    elif len(numK) == K:
                        subarrayA.append(A[right])
                        returnNum += 1
                    else:
                        break
                else:
                    if len(numK) < K:
                        subarrayA.append(A[right])
                    elif len(numK) == K:
                        subarrayA.append(A[right])
                        returnNum += 1
                right += 1
        return returnNum
```

超时。

## 其他解法

### 滑动窗口

```python
class Solution:
    def subarraysWithKDistinct(self, A: List[int], K: int) -> int:
        n = len(A)
        num1, num2 = collections.Counter(), collections.Counter()
        tot1 = tot2 = 0
        left1 = left2 = right = 0
        ret = 0

        for right, num in enumerate(A):
            if num1[num] == 0:
                tot1 += 1
            num1[num] += 1
            if num2[num] == 0:
                tot2 += 1
            num2[num] += 1
            
            while tot1 > K:
                num1[A[left1]] -= 1
                if num1[A[left1]] == 0:
                    tot1 -= 1
                left1 += 1
            while tot2 > K - 1:
                num2[A[left2]] -= 1
                if num2[A[left2]] == 0:
                    tot2 -= 1
                left2 += 1
            
            ret += left2 - left1
        
        return ret
```

#### 思路及算法

我们容易发现，对于任意一个右端点，可能存在一系列左端点与其对应，满足两端点所指区间对应的子数组内恰有 K 个不同整数。因此可能有 O(n^2)个子数组满足条件。因此无法暴力解决该题。

分析这些左端点，我们可以证明：对于任意一个右端点，能够与其对应的左端点们必然相邻。

证明非常直观，假设区间 $[l 1 ,r]$ 和 $[l 2 ,r]$ 为满足条件的数组（不失一般性，设$ l_1\leq l_2 $）。现在我们设存在一个$ l $满足 $l_1 \leq l \leq l_2$，那么区间 $[l,r] $作为 $[l_1,r]$ 的子数组，其中的不同整数数量必然不超过 K。同理，区间$[l,r]$ 作为$ [l 2 ,r] $的父数组，其中的不同整数数量必然不少于 K。那么可知区间 [l,r] 中的不同整数数量即为 K。

这样我们就可以用一个区间 $[l 1 ,l 2 ] $来代表能够与右端点 r 对应的左端点们。

同时，我们可以发现：当右端点向右移动时，左端点区间也同样向右移动。因为当我们在原有区间的右侧添加元素时，区间中的不同整数数量不会减少而只会不变或增加，因此我们需要在区间左侧删除一定元素，以保证区间内整数数量仍然为 K。

于是我们可以用滑动窗口解决本题，和普通的滑动窗口解法的不同之处在于，我们需要记录两个左指针 $\textit{left}_1$与 $\textit{left}_2$来表示左端点区间 $[\textit{left}_1,\textit{left}_2)$。第一个左指针表示极大的包含 K 个不同整数的区间的左端点，第二个左指针则表示极大的包含 K-1 个不同整数的区间的左端点。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组长度。我们至多只需要遍历该数组三次（右指针和两个左指针各一次）。

- 空间复杂度：O(n)，其中 n 是数组长度。我们需要记录每一个数的出现次数，本题中数的大小不超过数组长度。


### 转换

```python
class Solution:
    def subarraysWithKDistinct(self, A: List[int], K: int) -> int:
        return self.atMostK(A, K) - self.atMostK(A, K - 1)
        
    def atMostK(self, A, K):
        N = len(A)
        left, right = 0, 0
        counter = collections.Counter()
        distinct = 0
        res = 0
        while right < N:
            if counter[A[right]] == 0:
                distinct += 1
            counter[A[right]] += 1
            while distinct > K:
                counter[A[left]] -= 1
                if counter[A[left]] == 0:
                    distinct -= 1
                left += 1
            res += right - left + 1
            print(left, right, res)
            right += 1
        return res
```

```java
public class Solution {

    public int subarraysWithKDistinct(int[] A, int K) {
        return atMostKDistinct(A, K) - atMostKDistinct(A, K - 1);
    }

    /**
     * @param A
     * @param K
     * @return 最多包含 K 个不同整数的子区间的个数
     */
    private int atMostKDistinct(int[] A, int K) {
        int len = A.length;
        int[] freq = new int[len + 1];

        int left = 0;
        int right = 0;
        // [left, right) 里不同整数的个数
        int count = 0;
        int res = 0;
        // [left, right) 包含不同整数的个数小于等于 K
        while (right < len) {
            if (freq[A[right]] == 0) {
                count++;
            }
            freq[A[right]]++;
            right++;

            while (count > K) {
                freq[A[left]]--;
                if (freq[A[left]] == 0) {
                    count--;
                }
                left++;
            }
            // [left, right) 区间的长度就是对结果的贡献
            res += right - left;
        }
        return res;
    }
}
```

最初直觉使用双指针算法遇到的问题
对于一个固定的左边界来说，满足「恰好存在 K 个不同整数的子区间」的右边界 不唯一，且形成区间。

示例 1：左边界固定的时候，恰好存在 2 个不同整数的子区间为 [1,2],[1,2,1],[1,2,1,2]，总数为 3。其值为下标 3 - 1 + 1，即区间 [1..3] 的长度。

![image.png](https://pic.leetcode-cn.com/1612775858-VWbhYR-image.png)

须要找到左边界固定的情况下，满足「恰好存在 K 个不同整数的子区间」最小右边界和最大右边界。对比以前我们做过的，使用双指针解决的问题的问法基本都会出现「最小」、「最大」这样的字眼。

> **把原问题转换成为容易求解的问题**
> 友情提示：这里把 「恰好」 转换成为 「最多」须要一点求解「双指针（滑动窗口）」问题的经验。建立在熟练掌握这一类问题求解思路的基础上。

把「恰好」改成「最多」就可以使用双指针一前一后交替向右的方法完成，这是因为 对于每一个确定的左边界，最多包含 KK 种不同整数的右边界是唯一确定的，并且在左边界向右移动的过程中，右边界或者在原来的地方，或者在原来地方的右边。

而「最多存在 K 个不同整数的子区间的个数」与「恰好存在 K 个不同整数的子区间的个数」的差恰好等于「最多存在 K - 1个不同整数的子区间的个数」。

![image.png](https://pic.leetcode-cn.com/1612776085-sZFGqE-image.png)

因为原问题就转换成为求解「最多存在 K 个不同整数的子区间的个数」与 「最多存在 K - 1 个不同整数的子区间的个数」，它们其实是一个问题。

**方法：双指针（滑动窗口）**

实现函数 atMostWithKDistinct(A, K) ，表示「最多存在 K 个不同整数的子区间的个数」。于是 atMostWithKDistinct(A, K) - atMostWithKDistinct(A, K - 1) 即为所求。

**说明： res += right - left; 这行代码的意思：**

用具体的例子理解：最多包含 3 种不同整数的子区间 [1, 3, 2, 3] （双指针算法是在左边界固定的前提下，让右边界走到最右边），当前可以确定 1 开始的满足最多包含 3 种不同整数的子区间有 [1]、[1, 3]、[1, 3, 2]、[1, 3, 2, 3]。

所有的 左边界固定前提下，根据右边界最右的下标，计算出来的子区间的个数就是整个函数要返回的值。用右边界固定的前提下，左边界最左边的下标去计算也是完全可以的。

#### 复杂度分析：

- 时间复杂度：O(N)，这里 N 是输入数组的长度；
- 空间复杂度：O(N)，使用了常数个变量、频数数组的长度为 N + 1。

## 不清楚的函数

### [python3 Counter类（计数器]

Counter（计数器）：用于追踪值的出现次数

Counter类继承dict类，所以它能使用dict类里面的方法 

------

####  创建一个Counter类

```python
import collections
obj = collections.Counter('aabbccc')
print(obj)

#输出：Counter({'c': 3, 'a': 2, 'b': 2})
```

#### elements()

```python
import collections
obj = collections.Counter('aabbccc')
print(sorted(obj.elements()))

#输出：['a', 'a', 'b', 'b', 'c', 'c', 'c']

for k in obj.elements():   #遍历打印obj所有元素
    print(k)
```

#### most_common(指定一个参数n，列出前n个元素，不指定参数，则列出所有)

```python
import collections
obj = collections.Counter('aabbbcccc')
print(obj.most_common(2))

#输出：[('c', 4), ('b', 3)]
```

#### items(从dict类中继承的方法)

```python
import collections
obj = collections.Counter('aabbbcccc')
print(obj.items())

for k,v in obj.items():
    print(k,v)

#输出：dict_items([('b', 3), ('c', 4), ('a', 2)])
#     b 3
#     c 4
#     a 2
```

#### update(增加元素)

```python
import collections
obj = collections.Counter(['11','22'])
obj.update(['22','55'])
print(obj)

#输出：Counter({'22': 2, '11': 1, '55': 1})
```

#### subtract(原来的元素减去新传入的元素)

```python
import collections
obj = collections.Counter(['11','22','33'])
obj.subtract(['22','55'])
print(obj)

#输出：Counter({'11': 1, '33': 1, '22': 0, '55': -1})
```

### Python3 enumerate() 函数

####  描述

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

#### 语法

以下是 enumerate() 方法的语法:

```
enumerate(sequence, [start=0])
```

#### 参数

- sequence -- 一个序列、迭代器或其他支持迭代对象。
- start -- 下标起始位置。

#### 返回值

返回 enumerate(枚举) 对象。

------

#### 实例

以下展示了使用 enumerate() 方法的实例：

```python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))    # 小标从 1 开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```



#### 普通的 for 循环

```python
i = 0 
seq = ['one', 'two', 'three'] 
for element in seq:    
	print(i, seq[i])    
	i += 1
```

输出结果为：

```
0 one
1 two
2 three
```

#### for 循环使用 enumerate

```python
seq = ['one', 'two', 'three'] 
for i, element in enumerate(seq):    
	print(i, element)
```

输出结果为：

```
0 one
1 two
2 three
```