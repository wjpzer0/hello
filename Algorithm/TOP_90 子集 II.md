# 题目

给你一个整数数组 nums ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。

解集 不能 包含重复的子集。返回的解集中，子集可以按 任意顺序 排列。

示例 1：

```
输入：nums = [1,2,2]
输出：[[],[1],[1,2],[1,2,2],[2],[2,2]]
```


示例 2：

```
输入：nums = [0]
输出：[[],[0]]
```


提示：

- 1 <= nums.length <= 10
- -10 <= nums[i] <= 10

## 我的解法

未解出。

### 思路与算法

记原序列中元素的总数为 n。原序列中的每个数字 $a_i$的状态可能有两种，即「在子集中」和「不在子集中」。我们用 1 表示「在子集中」，0 表示不在子集中，那么每一个子集可以对应一个长度为 n 的 0/1 序列，第 i 位表示 $a_i$是否在子集中。例如，n = 3，a = \{ 5, 2, 9 \} 时：

| 0/1 序列 | 子集    | 0/1 序列对应的二进制数 |
| -------- | ------- | ---------------------- |
| 000      | \{ }    | 0                      |
| 001      | {9}     | 1                      |
| 010      | {2}     | 2                      |
| 011      | {2,9}   | 3                      |
| 100      | {5}     | 4                      |
| 101      | {5,9}   | 5                      |
| 110      | {5,2}   | 6                      |
| 111      | {5,2,9} | 7                      |


可以发现 0/1 序列对应的二进制数正好从 0 到 $2^n - 1$。我们可以枚举 $\textit{mask} \in [0, 2^n - 1]$，mask 的二进制表示是一个 0/1 序列，我们可以按照这个 0/1 序列在原集合当中取数。当我们枚举完所有 $2^n$个 mask，我们也就能构造出所有的子集。

### 查看题解后写出

```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        ans = []
        n = len(nums)
        nums.sort()
        for mask in range(1 << n):
            t = []
            flag = True
            for i in range(n):
                if (mask & (1 << i)) != 0:
                    if i > 0 and (mask >> (i-1) & 1) == 0 and nums[i] == nums[i-1]:
                        flag = False
                        break
                    t.append(nums[i])
            if flag:
                ans.append(t)
        return ans
```



## 其他解法

### 迭代法实现子集枚举

```java
class Solution {
    List<Integer> t = new ArrayList<Integer>();
    List<List<Integer>> ans = new ArrayList<List<Integer>>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        int n = nums.length;
        for (int mask = 0; mask < (1 << n); ++mask) {
            t.clear();
            boolean flag = true;
            for (int i = 0; i < n; ++i) {
                if ((mask & (1 << i)) != 0) {
                    if (i > 0 && (mask >> (i - 1) & 1) == 0 && nums[i] == nums[i - 1]) {
                        flag = false;
                        break;
                    }
                    t.add(nums[i]);
                }
            }
            if (flag) {
                ans.add(new ArrayList<Integer>(t));
            }
        }
        return ans;
    }
}
```

#### 思路

考虑数组 [1,2,2]，选择前两个数，或者第一、三个数，都会得到相同的子集。

也就是说，对于当前选择的数 x，若前面有与其相同的数 y，且没有选择 y，此时包含 x 的子集，必然会出现在包含 y 的所有子集中。

我们可以通过判断这种情况，来避免生成重复的子集。代码实现时，可以先将数组排序；迭代时，若发现没有选择上一个数，且当前数字与上一个数相同，则可以跳过当前生成的子集。

#### 复杂度分析

时间复杂度：$O(n \times 2^n)$，其中 n 是数组 nums 的长度。排序的时间复杂度为 O(nlogn)。一共 $2^n$个状态，每种状态需要 O(n) 的时间来构造子集，一共需要 $O(n \times 2^n)$的时间来构造子集。由于在渐进意义上 O(nlogn) 小于 $O(n \times 2^n)$，故总的时间复杂度为 $O(n \times 2^n)$。

空间复杂度：O(n)。即构造子集使用的临时数组 t 的空间代价。

### 递归法实现子集枚举

```java
class Solution {
    List<Integer> t = new ArrayList<Integer>();
    List<List<Integer>> ans = new ArrayList<List<Integer>>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        dfs(false, 0, nums);
        return ans;
    }

    public void dfs(boolean choosePre, int cur, int[] nums) {
        if (cur == nums.length) {
            ans.add(new ArrayList<Integer>(t));
            return;
        }
        dfs(false, cur + 1, nums);
        if (!choosePre && cur > 0 && nums[cur - 1] == nums[cur]) {
            return;
        }
        t.add(nums[cur]);
        dfs(true, cur + 1, nums);
        t.remove(t.size() - 1);
    }
}
```



#### 思路

与迭代法实现子集枚举类似，在递归时，若发现没有选择上一个数，且当前数字与上一个数相同，则可以跳过当前生成的子集。

#### 复杂度分析

时间复杂度：$O(n \times 2^n)$，其中 n 是数组 nums 的长度。排序的时间复杂度为 O(nlogn)。最坏情况下 nums 中无重复元素，需要枚举其所有 $2^n$个子集，每个子集加入答案时需要拷贝一份，耗时 O(n)，一共需要 $O(n \times 2^n)+O(n)=O(n \times 2^n)$的时间来构造子集。由于在渐进意义上 O(nlogn) 小于 $O(n \times 2^n)$，故总的时间复杂度为$ O(n \times 2^n)$。

空间复杂度：O(n)。临时数组 t 的空间代价是 O(n)，递归时栈空间的代价为 O(n)。

