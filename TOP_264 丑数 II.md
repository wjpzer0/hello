# 题目

给你一个整数 n ，请你找出并返回第 n 个 丑数 。

丑数 就是只包含质因数 2、3 和 5 的正整数。

示例 1：

```
输入：n = 10
输出：12
解释：[1, 2, 3, 4, 5, 6, 8, 9, 10, 12] 是由前 10 个丑数组成的序列。
```


示例 2：

```
输入：n = 1
输出：1
解释：1 通常被视为丑数。
```


提示：

1 <= n <= 1690

## 我的解法

```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        nums = [1]
        num = 2
        while True:
            if len(nums) == n:
                break
            mid = num
            for i in [2, 3, 5]:
                while mid % i == 0:
                    mid = mid / i
            if mid == 1:
                nums.append(num)
            num += 1
        return nums[-1]
```

超时，暴力破解。无解题思路。

## 其他解法

### 动态规划

```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        dp = [0] * (n + 1)
        dp[1] = 1
        p2 = p3 = p5 = 1

        for i in range(2, n + 1):
            num2, num3, num5 = dp[p2] * 2, dp[p3] * 3, dp[p5] * 5
            dp[i] = min(num2, num3, num5)
            if dp[i] == num2:
                p2 += 1
            if dp[i] == num3:
                p3 += 1
            if dp[i] == num5:
                p5 += 1
        
        return dp[n]
```

定义数组 dp，其中 dp[i] 表示第 i 个丑数，第 n 个丑数即为dp[n]。

由于最小的丑数是 1，因此 dp[1]=1。

如何得到其余的丑数呢？定义三个指针 $p_2,p_3,p_5$，表示下一个丑数是当前指针指向的丑数乘以对应的质因数。初始时，三个指针的值都是 1。

当 2≤i≤n 时，令 $\textit{dp}[i]=\min(\textit{dp}[p_2] \times 2, \textit{dp}[p_3] \times 3, \textit{dp}[p_5] \times 5)$，然后分别比较dp[i] 和$ \textit{dp}[p_2],\textit{dp}[p_3],\textit{dp}[p_5]$是否相等，如果相等则将对应的指针加 1。

#### 正确性证明

对于 i>1，在计算 dp[i] 时，指针 $p_x(x \in \{2,3,5\})$ 的含义是使得 $\textit{dp}[j] \times x>\textit{dp}[i-1]$的最小的下标 j，即当 $j \ge p_x$时 $\textit{dp}[j] \times x>\textit{dp}[i-1]$，当 $j<p_x$时 $\textit{dp}[j] \times x \le \textit{dp}[i-1]$。

因此，对于 i>1，在计算 dp[i] 时，$\textit{dp}[p_2] \times 2,\textit{dp}[p_3] \times 3,\textit{dp}[p_5] \times 5$都大于 dp[i−1]，$\textit{dp}[p_2-1] \times 2,\textit{dp}[p_3-1] \times 3,\textit{dp}[p_5-1] \times 5$都小于或等于 dp[i−1]。令 $\textit{dp}[i]=\min(\textit{dp}[p_2] \times 2, \textit{dp}[p_3] \times 3, \textit{dp}[p_5] \times 5)$，则 dp[i]>dp[i−1] 且dp[i] 是大于 dp[i−1] 的最小的丑数。

在计算 dp[i] 之后，会更新三个指针 $p_2,p_3,p_5$，更新之后的指针将用于计算 dp[i+1]，同样满足 dp[i+1]>dp[i] 且 dp[i+1] 是大于 dp[i] 的最小的丑数。

![img](https://assets.leetcode-cn.com/solution-static/264/p1.png)
![img](https://assets.leetcode-cn.com/solution-static/264/p2.png)
![img](https://assets.leetcode-cn.com/solution-static/264/p3.png)
![img](https://assets.leetcode-cn.com/solution-static/264/p4.png)
![img](https://assets.leetcode-cn.com/solution-static/264/p5.png)

#### 复杂度分析

- 时间复杂度：O(n)。需要计算数组 dp 中的 n 个元素，每个元素的计算都可以在 O(1)的时间内完成。

- 空间复杂度：O(n)。空间复杂度主要取决于数组 \dp 的大小。


### 最小堆

```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        factors = [2, 3, 5]
        seen = {1}
        heap = [1]

        for i in range(n - 1):
            curr = heapq.heappop(heap)
            for factor in factors:
                if (nxt := curr * factor) not in seen:
                    seen.add(nxt)
                    heapq.heappush(heap, nxt)

        return heapq.heappop(heap)
```

要得到从小到大的第 n 个丑数，可以使用最小堆实现。

初始时堆为空。首先将最小的丑数 1 加入堆。

每次取出堆顶元素 x，则 x 是堆中最小的丑数，由于 2x, 3x, 5x 也是丑数，因此将 2x, 3x, 5x 加入堆。

上述做法会导致堆中出现重复元素的情况。为了避免重复元素，可以使用哈希集合去重，避免相同元素多次加入堆。

在排除重复元素的情况下，第 n 次从最小堆中取出的元素即为第 n 个丑数。

![img](https://assets.leetcode-cn.com/solution-static/264/1.png)
![img](https://assets.leetcode-cn.com/solution-static/264/2.png)
![img](https://assets.leetcode-cn.com/solution-static/264/3.png)
![img](https://assets.leetcode-cn.com/solution-static/264/4.png)
![img](https://assets.leetcode-cn.com/solution-static/264/5.png)
![img](https://assets.leetcode-cn.com/solution-static/264/6.png)
![img](https://assets.leetcode-cn.com/solution-static/264/7.png)
![img](https://assets.leetcode-cn.com/solution-static/264/8.png)
![img](https://assets.leetcode-cn.com/solution-static/264/9.png)
![img](https://assets.leetcode-cn.com/solution-static/264/10.png)
![img](https://assets.leetcode-cn.com/solution-static/264/11.png)
![img](https://assets.leetcode-cn.com/solution-static/264/12.png)


#### 复杂度分析

- 时间复杂度：O(nlogn)。得到第 n 个丑数需要进行 n 次循环，每次循环都要从最小堆中取出 1 个元素以及向最小堆中加入最多 3 个元素，因此每次循环的时间复杂度是 O(logn+log3n)=O(logn)，总时间复杂度是 O(nlogn)。

- 空间复杂度：O(n)。空间复杂度主要取决于最小堆和哈希集合的大小，最小堆和哈希集合的大小都不会超过 3n。


