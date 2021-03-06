# 题目

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

示例:

```
输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```


说明:

假设你总是可以到达数组的最后一个位置。

## 我的解法

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        step = 0
        position = len(nums) - 1
        while position > 0:
            seat = 0
            while seat < position:
                if seat + nums[seat] >= position:
                    position = seat
                    step += 1
                    break
                seat += 1
        return step
```

看过题解后写的，想到递归，思路错了。比较好理解的算法，超时。

## 其他解法

### 反向查找出发位置

```java
public int jump(int[] nums) {
    int position = nums.length - 1; //要找的位置
    int steps = 0;
    while (position != 0) { //是否到了第 0 个位置
        for (int i = 0; i < position; i++) {
            if (nums[i] >= position - i) {
                position = i; //更新要找的位置
                steps++;
                break;
            }
        }
    }
    return steps;
}
```

我们的目标是到达数组的最后一个位置，因此我们可以考虑最后一步跳跃前所在的位置，该位置通过跳跃能够到达最后一个位置。

如果有多个位置通过跳跃都能够到达最后一个位置，那么我们应该如何进行选择呢？直观上来看，我们可以「贪心」地选择距离最后一个位置最远的那个位置，也就是对应下标最小的那个位置。因此，我们可以从左到右遍历数组，选择第一个满足要求的位置。

找到最后一步跳跃前所在的位置之后，我们继续贪心地寻找倒数第二步跳跃前所在的位置，以此类推，直到找到数组的开始位置。

这种想法看起来更简单了，为什么奏效呢？我们可以这样想。

![image.png](https://pic.leetcode-cn.com/0572d36a1a461720c2985bcb2f7a3c5d85826ead981d3364b5bf2efaea35e4ad-image.png)

从左到右跳的话，2 -> 3 -> 4 -> 1。

从右到左的话，我们找能跳到 1 的最左边的位置，我们找的只能是 4 或者是 4 左边的。

找到 4 的话，不用说，刚好完美。

如果是中间范围 3 和 4 之间的第 2 个 1 变成了 3，那么这个位置也可以跳到末尾的 1，按我们的算法我们就找到了这个 3，也就是 4 左边的位置。但其实并不影响我们的 steps，因为这个数字是 3 到 4 中间范围的数，左边界 3 也可以到这个数，所以下次找的话，会找到边界 3，或者边界 3 左边的数。 会不会直接找到上个边界 2 呢？不会的，如果找到了上一个边界 2，那么意味着从 2 直接跳到 3 和 4 之间的那个数，再从这个数跳到末尾就只需 2 步了，但是其实是需要 3 步的。

#### 复杂度分析

- 时间复杂度：O(n^2)，其中 n 是数组长度。有两层嵌套循环，在最坏的情况下，例如数组中的所有元素都是 1，position 需要遍历数组中的每个位置，对于 position 的每个值都有一次循环。

- 空间复杂度：O(1)。


### 正向查找可到达的最大位置

```python
class Solution:
    def jump(self, nums: List[int]) -> int:
        n = len(nums)
        maxPos, end, step = 0, 0, 0
        for i in range(n - 1):
            if maxPos >= i:
                maxPos = max(maxPos, i + nums[i])
                if i == end:
                    end = maxPos
                    step += 1
        return step
```

如果我们「贪心」地进行正向查找，每次找到可到达的最远位置，就可以在线性时间内得到最少的跳跃次数。

例如，对于数组 [2,3,1,2,4,2,3]，初始位置是下标 0，从下标 0 出发，最远可到达下标 2。下标 0 可到达的位置中，下标 1 的值是 3，从下标 1 出发可以达到更远的位置，因此第一步到达下标 1。

从下标 1 出发，最远可到达下标 4。下标 1 可到达的位置中，下标 4 的值是 4 ，从下标 4 出发可以达到更远的位置，因此第二步到达下标 4

![fig1](https://assets.leetcode-cn.com/solution-static/45/45_fig1.png)

在具体的实现中，我们维护当前能够到达的最大下标位置，记为边界。我们从左到右遍历数组，到达边界时，更新边界并将跳跃次数增加 1。

在遍历数组时，我们不访问最后一个元素，这是因为在访问最后一个元素之前，我们的边界一定大于等于最后一个位置，否则就无法跳到最后一个位置了。如果访问最后一个元素，在边界正好为最后一个位置的情况下，我们会增加一次「不必要的跳跃次数」，因此我们不必访问最后一个元素。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n是数组长度。
- 空间复杂度：O(1)。