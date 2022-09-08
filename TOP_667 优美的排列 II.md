# 题目

给你两个整数 `n` 和 `k` ，请你构造一个答案列表 `answer` ，该列表应当包含从 `1` 到 `n` 的 `n` 个不同正整数，并同时满足下述条件：

- 假设该列表是 `answer = [a1, a2, a3, ... , an]` ，那么列表 `[|a1 - a2|, |a2 - a3|, |a3 - a4|, ... , |an-1 - an|]` 中应该有且仅有 `k` 个不同整数。

返回列表 `answer` 。如果存在多种答案，只需返回其中 **任意一种** 。

**示例 1：**

```
输入：n = 3, k = 1
输出：[1, 2, 3]
解释：[1, 2, 3] 包含 3 个范围在 1-3 的不同整数，并且 [1, 1] 中有且仅有 1 个不同整数：1
```

**示例 2：**

```
输入：n = 3, k = 2
输出：[1, 3, 2]
解释：[1, 3, 2] 包含 3 个范围在 1-3 的不同整数，并且 [2, 1] 中有且仅有 2 个不同整数：1 和 2
```

**提示：**

- $1 <= k < n <= 10^4$

## 我的解法

确实没找到规律，查看大佬的评论后写出！

```
思路分析： 这道题就是找规律吧。
当n = 50， k = 20时：
[1,21,2,20,3,19,4,18,5,17,6,16,7,15,8,14,9,13,10,12,11,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50]
当n = 50，k = 17时：
[1,18,2,17,3,16,4,15,5,14,6,13,7,12,8,11,9,10,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50]
当n = 80，k = 30时：
[1,31,2,30,3,29,4,28,5,27,6,26,7,25,8,24,9,23,10,22,11,21,12,20,13,19,14,18,15,17,16,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80]
是不是发现了规律，就是
下标从[0, k]中，偶数下标填充[1,2,3…]，奇数下标填充[k + 1, k, k - 1…]，后面[k + 1, n - 1]都是顺序填充
[1, 1+k, 2, 1+k-1, 3, 1+k-2, 4.....k+2, k+3...n]
```

```python
class Solution:
    def constructArray(self, n: int, k: int) -> List[int]:
        ans = [0] * n
        numK = k + 1
        numTemp = 1
        for i in range(0, k + 1, 2):
            ans[i] = numTemp
            numTemp += 1
        for i in range(1, k + 1, 2):
            ans[i] = numK
            numK -= 1
        for i in range(k + 1, n):
            ans[i] = i + 1
        return ans 
```

## 其它解法

### 从特殊情况到一般情况

```c++
class Solution {
public:
    vector<int> constructArray(int n, int k) {
        vector<int> answer;
        for (int i = 1; i < n - k; ++i) {
            answer.push_back(i);
        }
        for (int i = n - k, j = n; i <= j; ++i, --j) {
            answer.push_back(i);
            if (i != j) {
                answer.push_back(j);
            }
        }
        return answer;
    }
};
```

```java
class Solution {
    public int[] constructArray(int n, int k) {
        int[] answer = new int[n];
        int idx = 0;
        for (int i = 1; i < n - k; ++i) {
            answer[idx] = i;
            ++idx;
        }
        for (int i = n - k, j = n; i <= j; ++i, --j) {
            answer[idx] = i;
            ++idx;
            if (i != j) {
                answer[idx] = j;
                ++idx;
            }
        }
        return answer;
    }
}
```

```python
class Solution:
    def constructArray(self, n: int, k: int) -> List[int]:
        answer = list(range(1, n - k))
        i, j = n - k, n
        while i <= j:
            answer.append(i)
            if i != j:
                answer.append(j)
            i, j = i + 1, j - 1
        return answer
```

#### 思路与算法

当 k=1 时，我们将 $1 \sim n$ 按照 $[1, 2, \cdots, n]$ 的顺序进行排列，那么相邻的差均为 1，满足 k=1 的要求。

当 k=n-1 时，我们将 $1 \sim n$ 按照 $[1, n, 2, n-1, 3, \cdots]$ 的顺序进行排列，那么相邻的差从 n-1 开始，依次递减 1。这样一来，所有从 1 到 n-1 的差值均出现一次，满足 k=n-1 的要求。

对于其它的一般情况，我们可以将这两种特殊情况进行合并，即列表的前半部分相邻差均为 1，后半部分相邻差从 k 开始逐渐递减到 1，这样从 1 到 k 的差值均出现一次，对应的列表即为：

$$
[1, 2, \cdots, n-k, n, n-k+1, n-1, n-k+2, \cdots]
$$

#### **复杂度分析**

- 时间复杂度：O(n)。
- 空间复杂度：O(1)，这里不计入返回值需要的空间。