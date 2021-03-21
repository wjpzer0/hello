# 题目

在仅包含 0 和 1 的数组 A 中，一次 K 位翻转包括选择一个长度为 K 的（连续）子数组，同时将子数组中的每个 0 更改为 1，而每个 1 更改为 0。

返回所需的 K 位翻转的最小次数，以便数组没有值为 0 的元素。如果不可能，返回 -1。

示例 1：

```
输入：A = [0,1,0], K = 1
输出：2
解释：先翻转 A[0]，然后翻转 A[2]。
```


示例 2：

```
输入：A = [1,1,0], K = 2
输出：-1
解释：无论我们怎样翻转大小为 2 的子数组，我们都不能使数组变为 [1,1,1]。
```


示例 3：

```
输入：A = [0,0,0,1,0,1,1,0], K = 3
输出：3
解释：
翻转 A[0],A[1],A[2]: A变成 [1,1,1,1,0,1,1,0]
翻转 A[4],A[5],A[6]: A变成 [1,1,1,1,1,0,0,0]
翻转 A[5],A[6],A[7]: A变成 [1,1,1,1,1,1,1,1]
```


提示：

1. 1 <= A.length <= 30000
2. 1 <= K <= A.length

## 我的解法

无，没有任何想法，解不出来。

### 解题思路

题目大意：每次翻转长度为 K 的子数组，求最少的翻转次数使数组中所有的 0 都更改为 1。如果不能实现，则返回 -1.

- 结论 1：后面区间的翻转，不会影响前面的元素。因此可以使用贪心策略，从左到右遍历，遇到每个 0 都把它和后面的 K 个数进行翻转。
- 结论 2：A[i] 翻转偶数次的结果是 A[i]；翻转奇数次的结果是 A[i] ^ 1。



## 其他解法

### 贪心算法

```python
class Solution(object):
    def minKBitFlips(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: int
        """
        N = len(A)
        res = 0
        for i in range(N - K + 1):
            if A[i] == 1:
                continue
            for j in range(K):
                A[i + j] ^= 1
            res += 1
        for i in range(N):
            if A[i] == 0:
                return -1
        return res
```

优化

```java
class Solution {
    public int minKBitFlips(int[] A, int K) {
        int ans = 0;
        int len = A.length;
        for(int i=0;i<len;i++){
            if(A[i] == 0){
                if(i + K > len) return -1;
                ans++;
                for(int j=i;j<i+K;j++)
                    A[j] ^= 1;
            }
        }
        return ans;
    }
}
```

当我们从某个位置开始准备翻转 K 位数字时，由于无法影响到该位置前面的数字，所以必须遇到 0 就翻转。不然该位置或者前面有 0 时，它无法变成 1。很明显该题具有贪心性质！因此我们的策略也很简单：只要遇到 0，就翻转从该位置开始的 K 位连续数字。

#### 复杂度分析

- 时间复杂度：O(N * K + N)，超时。
- 空间复杂度：O(1)。

### 差分数组

```java
class Solution {
    public int minKBitFlips(int[] A, int K) {
        int n = A.length;
        int[] diff = new int[n + 1];
        int ans = 0, revCnt = 0;
        for (int i = 0; i < n; ++i) {
            revCnt += diff[i];
            if ((A[i] + revCnt) % 2 == 0) {
                if (i + K > n) {
                    return -1;
                }
                ++ans;
                ++revCnt;
                --diff[i + K];
            }
        }
        return ans;
    }
}
```

由于模 22 意义下的加减法与异或等价，我们也可以用异或改写上面的代码。

```java
class Solution {
    public int minKBitFlips(int[] A, int K) {
        int n = A.length;
        int[] diff = new int[n + 1];
        int ans = 0, revCnt = 0;
        for (int i = 0; i < n; ++i) {
            revCnt ^= diff[i];
            if (A[i] == revCnt) { // A[i] ^ revCnt == 0
                if (i + K > n) {
                    return -1;
                }
                ++ans;
                revCnt ^= 1;
                diff[i + K] ^= 1;
            }
        }
        return ans;
    }
}
```

由于对同一个子数组执行两次翻转操作不会改变该子数组，所以对每个长度为 K 的子数组，应至多执行一次翻转操作。

对于若干个 K 位翻转操作，改变先后顺序并不影响最终翻转的结果。不妨从 A[0]开始考虑，若 A[0]=0，则必定要翻转从位置 0 开始的子数组；若 A[0]=1，则不翻转从位置 0 开始的子数组。

按照这一策略，我们从左到右地执行这些翻转操作。由于翻转操作是唯一的，若最终数组元素均为 1，则执行的翻转次数就是最小的。

用 N 表示数组 A 的长度。若直接模拟上述过程，复杂度将会是 O(NK) 的。如何优化呢？

考虑不去翻转数字，而是统计每个数字需要翻转的次数。对于一次翻转操作，相当于把子数组中所有数字的翻转次数加 1。

这启发我们用差分数组的思想来计算当前数字需要翻转的次数。我们可以维护一个差分数组 diff，其中 diff[i] 表示两个相邻元素 A[i−1] 和 A[i] 的翻转次数的差，对于区间 [l,r]，将其元素全部加 1，只会影响到 l 和 r+1 处的差分值，故 diff[l] 增加 1，diff[r+1] 减少 1。

通过累加差分数组可以得到当前位置需要翻转的次数，我们用变量 revCntrevCnt 来表示这一累加值。

遍历到 A[i] 时，若A[i]+revCnt 是偶数，则说明当前元素的实际值为 0，需要翻转区间 [i,i+K-1]，我们可以直接将 revCnt 增加 1，diff[i+K] 减少 1。

注意到若 i+K>n 则无法执行翻转操作，此时应返回 −1。

#### 复杂度分析

- 时间复杂度：O(N)，其中 N 是数组 A 的长度。需要对数组 AA 遍历一次。

- 空间复杂度：O(N)，其中 N 是数组 A 的长度。需要创建一个长度为 N+1 的差分数组 diff。


### 滑动窗口

```java
class Solution {
    public int minKBitFlips(int[] A, int K) {
        int n = A.length;
        int ans = 0, revCnt = 0;
        for (int i = 0; i < n; ++i) {
            if (i >= K && A[i - K] > 1) {
                revCnt ^= 1;
                A[i - K] -= 2; // 复原数组元素，若允许修改数组 A，则可以省略
            }
            if (A[i] == revCnt) {
                if (i + K > n) {
                    return -1;
                }
                ++ans;
                revCnt ^= 1;
                A[i] += 2;
            }
        }
        return ans;
    }
}
```

能否将空间复杂度优化至 O(1) 呢？

回顾差分数组的代码，当遍历到位置 i 时，若能知道位置 i-K上发生了翻转操作，便可以直接修改 revCnt，从而去掉 diff 数组。

注意到 0≤A[i]≤1，我们可以用 A[i] 范围之外的数来表达「是否翻转过」的含义。

具体来说，若要翻转从位置 i 开始的子数组，可以将 A[i] 加 2，这样当遍历到位置 i'时，若有 A[i'-K]>1，则说明在位置 i'-K上发生了翻转操作。

#### 复杂度分析

- 时间复杂度：O(N)。其中 N 是数组 A的长度。需要对数组 A遍历一次。
- 空间复杂度：O(1)。

### 滑动窗口2

```python
class Solution(object):
    def minKBitFlips(self, A, K):
        """
        :type A: List[int]
        :type K: int
        :rtype: int
        """
        N = len(A)
        que = collections.deque()
        res = 0
        for i in range(N):
            if que and i >= que[0] + K:
                que.popleft()
            if len(que) % 2 == A[i]:
                if i +  K > N: return -1
                que.append(i)
                res += 1
        return res
```

上面方法超时的主要原因是我们真实地进行了翻转。根据结论二，位置 i 现在的状态，和它被前面 K - 1 个元素翻转的次数（奇偶性）有关。

我们使用队列模拟滑动窗口，该滑动窗口的含义是前面 K - 1 个元素中，以哪些位置起始的 子区间进行了翻转。该滑动窗口从左向右滑动，如果当前位置 i 需要翻转，则把该位置存储到队列中。遍历到新位置 j (j < i + K) 时，队列中元素的个数代表了 i 被前面 K - 1 个元素翻转的次数。

- 当 i 位置被翻转了偶数次，如果 A[i] 为 0，那么翻转后仍是 0，当前元素需要翻转；
- 当 i 位置被翻转了奇数次，如果 A[i] 为 1，那么翻转后是 0，当前元素需要翻转。

综合上面两点，我们得到一个结论，如果 len(que) % 2 == A[i] 时，当前元素需要翻转。

当 i + K > N 时，说明需要翻转大小为 K 的子区间，但是后面剩余的元素不到 K 个了，所以返回 -1。

#### 示例

下面的动图演示了题目给出的示例三 A = [0,0,0,1,0,1,1,0], K = 3 的情况：

![995.gif](https://pic.leetcode-cn.com/1613618561-CIjAhM-995.gif)

#### 复杂度分析

- 时间复杂度：O(N)。
- 空间复杂度：O(K)。