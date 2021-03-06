# 题目

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，能够偷窃到的最高金额。

示例 1：

```
输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```


示例 2：

```
输入：nums = [1,2,3,1]
输出：4
解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
```


示例 3：

```
输入：nums = [0]
输出：0
```


提示：

- 1 <= nums.length <= 100
- 0 <= nums[i] <= 1000

## 我的解法

未解出，以后没有思路先想想动态规划！

## 其他解法

### 动态规划

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        def robRange(start: int, end: int) -> int:
            first = nums[start]
            second = max(nums[start], nums[start + 1])
            for i in range(start + 2, end + 1):
                first, second = second, max(first + nums[i], second)
            return second
        
        length = len(nums)
        if length == 1:
            return nums[0]
        elif length == 2:
            return max(nums[0], nums[1])
        else:
            return max(robRange(0, length - 2), robRange(1, length - 1))
```

```Java
class Solution {
    public int rob(int[] nums) {
        int length = nums.length;
        if (length == 1) {
            return nums[0];
        } else if (length == 2) {
            return Math.max(nums[0], nums[1]);
        }
        return Math.max(robRange(nums, 0, length - 2), robRange(nums, 1, length - 1));
    }

    public int robRange(int[] nums, int start, int end) {
        int first = nums[start], second = Math.max(nums[start], nums[start + 1]);
        for (int i = start + 2; i <= end; i++) {
            int temp = second;
            second = Math.max(first + nums[i], second);
            first = temp;
        }
        return second;
    }
}
```

```c++
class Solution {
public:
    int robRange(vector<int>& nums, int start, int end) {
        int first = nums[start], second = max(nums[start], nums[start + 1]);
        for (int i = start + 2; i <= end; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }

    int rob(vector<int>& nums) {
        int length = nums.size();
        if (length == 1) {
            return nums[0];
        } else if (length == 2) {
            return max(nums[0], nums[1]);
        }
        return max(robRange(nums, 0, length - 2), robRange(nums, 1, length - 1));
    }
};
```

首先考虑最简单的情况。如果只有一间房屋，则偷窃该房屋，可以偷窃到最高总金额。如果只有两间房屋，则由于两间房屋相邻，不能同时偷窃，只能偷窃其中的一间房屋，因此选择其中金额较高的房屋进行偷窃，可以偷窃到最高总金额。

注意到当房屋数量不超过两间时，最多只能偷窃一间房屋，因此不需要考虑首尾相连的问题。如果房屋数量大于两间，就必须考虑首尾相连的问题，第一间房屋和最后一间房屋不能同时偷窃。

如何才能保证第一间房屋和最后一间房屋不同时偷窃呢？如果偷窃了第一间房屋，则不能偷窃最后一间房屋，因此偷窃房屋的范围是第一间房屋到最后第二间房屋；如果偷窃了最后一间房屋，则不能偷窃第一间房屋，因此偷窃房屋的范围是第二间房屋到最后一间房屋。

假设数组 nums 的长度为 n。如果不偷窃最后一间房屋，则偷窃房屋的下标范围是 [0, n-2]；如果不偷窃第一间房屋，则偷窃房屋的下标范围是 [1, n-1]。在确定偷窃房屋的下标范围之后，即可用第 198 题的方法解决。对于两段下标范围分别计算可以偷窃到的最高总金额，其中的最大值即为在 nn 间房屋中可以偷窃到的最高总金额。

假设偷窃房屋的下标范围是 [start,end]，用 \textit{dp}[i]dp[i] 表示在下标范围 [start,i] 内可以偷窃到的最高总金额，那么就有如下的状态转移方程：

$$
\textit{dp}[i] = \max(\textit{dp}[i-2]+\textit{nums}[i], \textit{dp}[i-1])
$$
边界条件为：

$$
\begin{cases} \textit{dp}[\textit{start}] = \textit{nums}[\textit{start}] & 只有一间房屋，则偷窃该房屋 \\ \textit{dp}[\textit{start}+1] = \max(\textit{nums}[\textit{start}], \textit{nums}[\textit{start}+1]) & 只有两间房屋，偷窃其中金额较高的房屋 \end{cases}
$$

计算得到 dp[end] 即为下标范围 [start,end] 内可以偷窃到的最高总金额。

分别取 (start,end)=(0,n−2) 和 (start,end)=(1,n−1) 进行计算，取两个 dp[end] 中的最大值，即可得到最终结果。

根据上述思路，可以得到时间复杂度 O(n)和空间复杂度 O(n) 的实现。考虑到每间房屋的最高总金额只和该房屋的前两间房屋的最高总金额相关，因此可以使用滚动数组，在每个时刻只需要存储前两间房屋的最高总金额，将空间复杂度降到 O(1)。

![img](https://assets.leetcode-cn.com/solution-static/213/1.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/2.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/3.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/4.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/5.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/6.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/7.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/8.PNG)
![img](https://assets.leetcode-cn.com/solution-static/213/9.PNG)


#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组长度。需要对数组遍历两次，计算可以偷窃到的最高总金额。
- 空间复杂度：O(1)。

