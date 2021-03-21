# 题目

给定 n 个整数，找出平均数最大且长度为 k 的连续子数组，并输出该最大平均数。

示例：

```
输入：[1,12,-5,-6,50,3], k = 4
输出：12.75
解释：最大平均数 (12-5-6+50)/4 = 51/4 = 12.75
```


提示：

1 <= k <= n <= 30,000。
所给数据范围 [-10,000，10,000]。

## 我的解法

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        maxList = sum(nums[:k])
        end = k
        while end < len(nums):
            temporary = sum(nums[end+1-k:end+1])
            maxList = max(temporary, maxList)
            end += 1
        return maxList / k
```

时间复杂度过高，超时，可以简化

## 其他解法

### 滑动窗口

```python
class Solution:
    def findMaxAverage(self, nums: List[int], k: int) -> float:
        maxTotal = total = sum(nums[:k])
        n = len(nums)

        for i in range(k, n):
            total = total - nums[i - k] + nums[i]
            maxTotal = max(maxTotal, total)
        
        return maxTotal / k
```

由于规定了子数组的长度为 k，因此可以通过寻找子数组的最大元素和的方式寻找子数组的最大平均数，元素和最大的子数组对应的平均数也是最大的。证明如下：

> 假设两个不同的子数组的长度都是 k，这两个子数组的元素和分别是 x 和 y，则这两个子数组的平均数分别是 x/k 和 y/k。如果 x≥y，则有 x/k≥y/k，即如果一个子数组的元素和更大，则该子数组的平均数也更大。
>

为了找到子数组的最大元素和，需要对数组中的每个长度为 k 的子数组分别计算元素和。对于长度为 n 的数组，当 k≤n 时，有 n−k+1 个长度为 k 的子数组。如果直接计算每个子数组的元素和，则时间复杂度过高，无法通过全部测试用例，因此需要使用时间复杂度更低的方法计算每个子数组的元素和。

用 $\textit{sum}_i$表示数组 nums 以下标 i 结尾的长度为 k 的子数组的元素和，其中 i≥k−1，则 $\textit{sum}_i$的计算方法如下：

$$
\textit{sum}_i=\sum\limits_{j=i-k+1}^i \textit{nums}[j]
$$
当 i>k−1 时，将上式的 i 替换成 i-1，可以得到以下算式：

$$
\textit{sum}_{i-1}=\sum\limits_{j=i-k}^{i-1} \textit{nums}[j]
$$
将上述两个算式相减，可以得到如下关系：

$$
\textit{sum}_i-\textit{sum}_{i-1}=\textit{nums}[i]-\textit{nums}[i-k]
$$
整理得到：

$$
\textit{sum}_i=\textit{sum}_{i-1}-\textit{nums}[i-k]+\textit{nums}[i]
$$
上述过程可以看成维护一个长度为 kk 的滑动窗口。当滑动窗口从下标范围 [i-k,i-1] 移动到下标范围 [i-k+1,i] 时，nums[i−k] 从窗口中移出，nums[i] 进入到窗口内。

利用上述关系，可以在 O(1) 的时间内通过 $\textit{sum}_{i-1}$得到 $\textit{sum}_i$。

第一个子数组的元素和 $\textit{sum}_{k-1}$需要通过计算nums 的前 k 个元素之和得到，从 $\textit{sum}_k$到 $\textit{sum}_{n-1}$的值则可利用上述关系快速计算得到。只需要遍历数组 nums 一次即可得到每个长度为 k 的子数组的元素和，时间复杂度是 O(n)。

在上述过程中维护最大的子数组元素和，记为 maxSum，子数组的最大平均数即为 kmaxSum/k。需要注意返回值是浮点型，因此计算除法时需要进行数据类型转换。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 nums 的长度。遍历数组一次。
- 空间复杂度：O(1)。

