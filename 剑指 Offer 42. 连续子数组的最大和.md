# 题目

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。

**示例1:**

```
输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**提示：**

- `1 <= arr.length <= 10^5`
- `-100 <= arr[i] <= 100`

注意：本题与主站 53 题相同：https://leetcode-cn.com/problems/maximum-subarray/

## 我的解法

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        pre = [0]
        for i in nums:
            pre.append(pre[-1] + i)

        print(pre)
        res = float("-inf")
        l = len(pre)
        for i in range(1, l):
            minSum = min(pre[:i])
            midSum = pre[i]
            midr = midSum - minSum
            res = max(res, midr)

        return res
```

超时！，我是菜鸡！

## 其他解法

### 动态规划

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int pre = 0, maxAns = nums[0];
        for (const auto &x: nums) {
            pre = max(pre + x, x);
            maxAns = max(maxAns, pre);
        }
        return maxAns;
    }
};
```

```
class Solution {
    public int maxSubArray(int[] nums) {
        int res = nums[0];
        for(int i = 1; i < nums.length; i++) {
            nums[i] += Math.max(nums[i - 1], 0);
            res = Math.max(res, nums[i]);
        }
        return res;
    }
}
```

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        for i in range(1, len(nums)):
            nums[i] += max(nums[i - 1], 0)
        return max(nums)
```

#### 动态规划解析：

- **状态定义**： 设动态规划列表 dp ，dp[i] 代表以元素 nums[i] 为结尾的连续子数组最大和。
	- 为何定义最大和 dp[i] 中必须包含元素 nums[i] ：保证 dp[i] 递推到 dp[i+1] 的正确性；如果不包含 nums[i] ，递推时则不满足题目的 连续子数组 要求。
- **转移方程**： 若 $dp[i-1] \leq 0$ ，说明 dp[i - 1] 对 dp[i] 产生负贡献，即 $dp[i-1] + nums[i]$ 还不如 nums[i] 本身大。
	- 当 $dp[i - 1] > 0$ 时：执行 $dp[i] = dp[i-1] + nums[i]$ ；
	- 当 $dp[i - 1] \leq 0$ 时：执行 $dp[i] = nums[i]$ ；
- **初始状态**： dp[0] = nums[0]dp[0]=nums[0]，即以 nums[0]nums[0] 结尾的连续子数组最大和为 nums[0]nums[0] 。
- **返回值**： 返回 dp 列表中的最大值，代表全局最大值。

![Picture1.png](https://pic.leetcode-cn.com/8fec91e89a69d8695be2974de14b74905fcd60393921492bbe0338b0a628fd9a-Picture1.png)

空间复杂度降低：

- 由于 dp[i] 只与 dp[i−1] 和 nums[i] 有关系，因此可以将原数组 nums 用作 dp列表，即直接在 nums 上修改即可。
- 由于省去 dp 列表使用的额外空间，因此空间复杂度从 O(N) 降至 O(1) 。

![img](https://pic.leetcode-cn.com/ea600eec9a9b413dfbf41ead44859ec77b4473131b5103e37c87528e5d6d1b79-Picture2.png)
![img](https://pic.leetcode-cn.com/7eab2e48f63d9668b25d5c53a5fb32f069b10fcff518e8d2f822872ea6bfe0b7-Picture3.png)
![img](https://pic.leetcode-cn.com/3448a2dc8c3795265fad5dd555f347c6852858677de2a911ee250aabd6cd70cb-Picture4.png)
![img](https://pic.leetcode-cn.com/d52adac54284cd1aaf893d15be870923166472763da798c0a118258264bff5aa-Picture5.png)
![img](https://pic.leetcode-cn.com/521ede63f1346e2cf60db0002d7f03d34726c998ba5f609e7eb0de01b2789b3f-Picture6.png)
![img](https://pic.leetcode-cn.com/53a1e1f33efd1367635f7237a5ece0d62c727c3ba565167d2ecb19e04afaa57a-Picture7.png)
![img](https://pic.leetcode-cn.com/8dac6fb2c89a7aa3ccbdf89782a54a8b5df7cab0550e6b5853390a48d3edc946-Picture8.png)
![img](https://pic.leetcode-cn.com/1c08d6263238b970c9780b2e3ee9d9f8ab64ea1b977dbb4a0103ba0a94bcd346-Picture9.png)
![img](https://pic.leetcode-cn.com/b37120c2a46faef1743a63b5924463526b9b4c2b934ac3b8b36e09bc286817cc-Picture10.png)
![img](https://pic.leetcode-cn.com/c502664e32644efb8ffd25e6b118a9c754803beddfd09ca35fcc22c50d7c8d53-Picture11.png)
#### 复杂度分析

- 时间复杂度 O(N)： 线性遍历数组 nums 即可获得结果，使用 O(N) 时间。
- 空间复杂度 O(1) ： 使用常数大小的额外空间。

### 分治

```c++
class Solution {
public:
    struct Status {
        int lSum, rSum, mSum, iSum;
    };

    Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = max(l.lSum, l.iSum + r.lSum);
        int rSum = max(r.rSum, r.iSum + l.rSum);
        int mSum = max(max(l.mSum, r.mSum), l.rSum + r.lSum);
        return (Status) {lSum, rSum, mSum, iSum};
    };

    Status get(vector<int> &a, int l, int r) {
        if (l == r) {
            return (Status) {a[l], a[l], a[l], a[l]};
        }
        int m = (l + r) >> 1;
        Status lSub = get(a, l, m);
        Status rSub = get(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    int maxSubArray(vector<int>& nums) {
        return get(nums, 0, nums.size() - 1).mSum;
    }
};
```

```java
class Solution {
    public class Status {
        public int lSum, rSum, mSum, iSum;

        public Status(int lSum, int rSum, int mSum, int iSum) {
            this.lSum = lSum;
            this.rSum = rSum;
            this.mSum = mSum;
            this.iSum = iSum;
        }
    }

    public int maxSubArray(int[] nums) {
        return getInfo(nums, 0, nums.length - 1).mSum;
    }

    public Status getInfo(int[] a, int l, int r) {
        if (l == r) {
            return new Status(a[l], a[l], a[l], a[l]);
        }
        int m = (l + r) >> 1;
        Status lSub = getInfo(a, l, m);
        Status rSub = getInfo(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    public Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = Math.max(l.lSum, l.iSum + r.lSum);
        int rSum = Math.max(r.rSum, r.iSum + l.rSum);
        int mSum = Math.max(Math.max(l.mSum, r.mSum), l.rSum + r.lSum);
        return new Status(lSum, rSum, mSum, iSum);
    }
}
```

#### 思路和算法

**这个分治方法类似于「线段树求解最长公共上升子序列问题」的 pushUp 操作。** 也许读者还没有接触过线段树，没有关系，方法二的内容假设你没有任何线段树的基础。当然，如果读者有兴趣的话，推荐阅读线段树区间合并法解决多次询问的「区间最长连续上升序列问题」和「区间最大子段和问题」，还是非常有趣的。

我们定义一个操作 get(a, l, r) 表示查询 a 序列 $[l,r]$ 区间内的最大子段和，那么最终我们要求的答案就是 get(nums, 0, nums.size() - 1)。如何分治实现这个操作呢？对于一个区间 $[l,r]$，我们取 $m = \lfloor \frac{l + r}{2} \rfloor$，对区间 $[l,m]$ 和 $[m+1,r]$ 分治求解。当递归逐层深入直到区间长度缩小为 1 的时候，递归「开始回升」。这个时候我们考虑如何通过$ [l,m]$ 区间的信息和 $[m+1,r]$ 区间的信息合并成区间 $[l,r]$ 的信息。最关键的两个问题是：

- 我们要维护区间的哪些信息呢？
- 我们如何合并这些信息呢？

对于一个区间 $[l,r]$，我们可以维护四个量：

$\textit{lSum}$ 表示 $[l,r]$ 内以 l 为左端点的最大子段和

$\textit{rSum}$ 表示 $[l,r]$ 内以 r 为右端点的最大子段和

$\textit{mSum}$ 表示 $[l,r]$ 内的最大子段和

$\textit{iSum}$ 表示 $[l,r]$ 的区间和

以下简称 $[l,m]$ 为 $[l,r]$ 的「左子区间」，$[m+1,r]$为 $[l,r]$ 的「右子区间」。我们考虑如何维护这些量呢（如何通过左右子区间的信息合并得到 $[l,r]$ 的信息）？对于长度为 1 的区间 $[i, i]$，四个量的值都和 $\textit{nums}[i]$ 相等。对于长度大于 1 的区间：

- 首先最好维护的是 $\textit{iSum}$，区间 $[l,r]$ 的 $\textit{iSum}$ 就等于「左子区间」的 $\textit{iSum}$ 加上「右子区间」的 $\textit{iSum}$。
- 对于 $[l,r]$ 的 $\textit{lSum}$，存在两种可能，它要么等于「左子区间」的 $\textit{lSum}$，要么等于「左子区间」的 $\textit{iSum}$ 加上「右子区间」的 $\textit{lSum}$，二者取大。
- 对于 $[l,r]$ 的 $\textit{rSum}$，同理，它要么等于「右子区间」的 $\textit{rSum}$，要么等于「右子区间」的 $\textit{iSum}$ 加上「左子区间」的 $\textit{rSum}$，二者取大。
- 当计算好上面的三个量之后，就很好计算 $[l,r]$ 的 $\textit{mSum}$ 了。我们可以考虑 $[l,r]$ 的 $\textit{mSum}$ 对应的区间是否跨越 m——它可能不跨越 m，也就是说 $[l,r]$的 $\textit{mSum}$ 可能是「左子区间」的 $\textit{mSum}$ 和 「右子区间」的 $\textit{mSum}$ 中的一个；它也可能跨越 m，可能是「左子区间」的 $\textit{rSum}$ 和 「右子区间」的 $\textit{lSum}$ 求和。三者取大。
  这样问题就得到了解决。

#### 复杂度分析

假设序列 a 的长度为 n。

- 时间复杂度：假设我们把递归的过程看作是一颗二叉树的先序遍历，那么这颗二叉树的深度的渐进上界为 $O(\log n)$，这里的总时间相当于遍历这颗二叉树的所有节点，故总时间的渐进上界是 O$(\sum_{i=1}^{\log n} 2^{i-1})=O(n)$，故渐进时间复杂度为 O(n)。
- 空间复杂度：递归会使用 $O(\log n)$ 的栈空间，故渐进空间复杂度为 $O(\log n)$。

