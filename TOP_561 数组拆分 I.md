# 题目

给定长度为 2n 的整数数组 nums ，你的任务是将这些数分成 n 对, 例如 (a1, b1), (a2, b2), ..., (an, bn) ，使得从 1 到 n 的 min(ai, bi) 总和最大。

返回该 最大总和 。



示例 1：

```
  输入：nums = [1,4,3,2]
  输出：4
  解释：所有可能的分法（忽略元素顺序）为：
  
  1. (1, 4), (2, 3) -> min(1, 4) + min(2, 3) = 1 + 2 = 3
  2. (1, 3), (2, 4) -> min(1, 3) + min(2, 4) = 1 + 2 = 3
  3. (1, 2), (3, 4) -> min(1, 2) + min(3, 4) = 1 + 3 = 4
     所以最大总和为 4
```


  示例 2：

```
输入：nums = [6,2,6,5,1,2]
输出：9
解释：最优的分法为 (2, 1), (2, 5), (6, 6). min(2, 1) + min(2, 5) + min(6, 6) = 1 + 2 + 6 = 9
```


提示：

- 1 <= n <= $10^4$
- nums.length == 2 * n
- $-10^4 $<= nums[i] <= $10^4$

## 我的解法

```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        nums.sort()
        sumNum = 0
        for i in range(0, len(nums), 2):
            sumNum += min(nums[i], nums[i+1])
        return sumNum
```

可精简代码。

## 其他解法

### 排序

```python
class Solution:
    def arrayPairSum(self, nums: List[int]) -> int:
        nums.sort()
        return sum(nums[::2])
```



#### 思路与算法

不失一般性，我们令每一组 $(a_i, b_i)$满足 $a_i \leq b_i$（若不满足，交换二者即可），这样我们需要求得的总和

$$
\sum_{i=1}^n \min(a_i, b_i)
$$
就等于所有 $a_i$的和

$$
\sum_{i=1}^n a_i \tag{1}
$$
接下来，我们将所有的 $(a_i, b_i)$按照升序排序，使得 $a_1 \leq a_2 \leq \cdots \leq a_n $。这样一来，对于任意的 $a_j$

- 它不大于 $a_{j+1}, a_{j+2}, \cdots, a_na $；
- 它不大于$ b_j$；

- 由于 $a_i \leq b_i$对于任意的 ii 恒成立，因此它不大于 $b_{j+1}, b_{j+2}, \cdots, b_n$。

由于 $a_j $不大于 \{a\} 中的 n-j个元素，也不大于 {b} 中的 n-j+1 个元素，而这些元素都是从 $\textit{nums}$ 中而来的，因此 $a_j$在数组$ \textit{nums}$ 中「从大到小」至少排在第 (n-j) + (n-j+1) + 1 = 2(n-j+1)个位置，也就是「从小到大」至多排在第 2n - 2(n-j+1) + 1 = 2(j-1) + 1 个位置，这里位置的编号从 1 开始，即
$$
a_j \leq c_{2(j-1)+1}
$$

其中数组 cc 是将数组 \textit{nums}nums 升序排序得到的结果，代入 (1)(1) 式即可得到

$$
\sum_{i=1}^n a_i \leq \sum_{i=1}^n c_{2(i-1)+1} \tag{2}
$$
另一方面，令 $(a_1, b_1) = (c_1, c_2), (a_2, b_2) = (c_3, c_4), \cdots, (a_n, b_n) = (c_{2n-1}, c_{2n})$，此时每一组 $(a_i, b_i)$都满足 $a_i \leq b_i$的要求，并且有$ a_1 \leq a_2 \leq \cdots \leq a_n$，此时
$$
\sum_{i=1}^n a_i = \sum_{i=1}^n c_{2(i-1)+1}
$$

即 (2) 式的等号是可满足的。因此所要求得的最大总和即为

$$
\sum_{i=1}^n c_{2(i-1)+1}
$$

代码

需要注意大部分语言的数组编号是从 0（而不是上文中的 1）开始的。

复杂度分析

时间复杂度：$O(n\log n)$，即为对数组 $\textit{nums}$ 进行排序的时间复杂度。

空间复杂度：$O(\log n)$，即为排序需要使用的栈空间。

