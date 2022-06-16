# 题目

给定一个整数数组和一个整数 `**k**`，你需要在数组里找到 **不同的** k-diff 数对，并返回不同的 **k-diff 数对** 的数目。

这里将 **k-diff** 数对定义为一个整数对 `(nums[i], nums[j])`，并满足下述全部条件：

- `0 <= i < j < nums.length`
- `|nums[i] - nums[j]| == k`

**注意**，`|val|` 表示 `val` 的绝对值。

**示例 1：**

```
输入：nums = [3, 1, 4, 1, 5], k = 2
输出：2
解释：数组中有两个 2-diff 数对, (1, 3) 和 (3, 5)。
尽管数组中有两个1，但我们只应返回不同的数对的数量。
```

**示例 2：**

```
输入：nums = [1, 2, 3, 4, 5], k = 1
输出：4
解释：数组中有四个 1-diff 数对, (1, 2), (2, 3), (3, 4) 和 (4, 5)。
```

**示例 3：**

```
输入：nums = [1, 3, 1, 5, 4], k = 0
输出：1
解释：数组中只有一个 0-diff 数对，(1, 1)。
```

**提示：**

- $1 <= nums.length <= 10^4$
- $-10^7 <= nums[i] <= 10^7$
- $0 <= k <= 10^7$

## 我的解法

思路没有理清楚，k==0时进行特判！

```python
class Solution:
    def findPairs(self, nums: List[int], k: int) -> int:
        if k == 0:
            mapNum = {}
            for num in nums:
                if num in mapNum:
                    mapNum[num] += 1
                else:
                    mapNum[num] = 1
            ans = 0
            for num, n in mapNum.items():
                if n > 1:
                    ans += 1
            return ans
        setNum = set(nums)
        sortNum = list(setNum)
        print(setNum)
        print(sortNum)
        ans = 0
        for num in sortNum:
            print(num)
            if num + k in setNum:
                ans += 1
        return ans
```

## 其他解法

### 哈希表

```c++
class Solution {
public:
    int findPairs(vector<int>& nums, int k) {
        unordered_set<int> visited;
        unordered_set<int> res;
        for (int num : nums) {
            if (visited.count(num - k)) {
                res.emplace(num - k);
            }
            if (visited.count(num + k)) {
                res.emplace(num);
            }
            visited.emplace(num);
        }
        return res.size();
    }
};
```

```java
class Solution {
    public int findPairs(int[] nums, int k) {
        Set<Integer> visited = new HashSet<Integer>();
        Set<Integer> res = new HashSet<Integer>();
        for (int num : nums) {
            if (visited.contains(num - k)) {
                res.add(num - k);
            }
            if (visited.contains(num + k)) {
                res.add(num);
            }
            visited.add(num);
        }
        return res.size();
    }
}
```

```python
class Solution:
    def findPairs(self, nums: List[int], k: int) -> int:
        visited, res = set(), set()
        for num in nums:
            if num - k in visited:
                res.add(num - k)
            if num + k in visited:
                res.add(num)
            visited.add(num)
        return len(res) 
```

#### 思路

遍历数组，找出符合条件的数对。因为是寻找不同的数对，所以可以将数对放入哈希表$ \textit{res}$，完成去重的效果，最后返回哈希表的长度即可。遍历数组时，可以将遍历到的下标当作潜在的 j，判断 j 左侧是否有满足条件的 i 来构成 k-diff 数对，而这一判断也可以通过提前将下标 j 左侧的元素都放入另一个哈希表 $\textit{visited}$来降低时间复杂度。如果可以构成，则将数对放入哈希表 $\textit{res}$。

代码实现时，由于 k 是定值，知道数对的较小值，也就知道了另一个值，因此我们可以只将数对的较小值放入 $\textit{res}$，而不影响结果的正确性。判断完之后，再将当前元素放入 $\textit{visited}$，作为后续判断潜在的 $\textit{nums}[i]$。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 $\text{nums}$的长度。需要遍历 $\text{nums}$一次。

- 空间复杂度：O(n)，其中 n 是数组 $\text{nums}$的长度。两个哈希表最多各存放 n 个元素。


### 排序 + 双指针

```c++
class Solution {
public:
    int findPairs(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        int n = nums.size(), y = 0, res = 0;
        for (int x = 0; x < n; x++) {
            if (x == 0 || nums[x] != nums[x - 1]) {
                while (y < n && (nums[y] < nums[x] + k || y <= x)) {
                    y++;
                }
                if (y < n && nums[y] == nums[x] + k) {
                    res++;
                }
            }
        }
        return res;
    }
};
```

```java
class Solution {
    public int findPairs(int[] nums, int k) {
        Arrays.sort(nums);
        int n = nums.length, y = 0, res = 0;
        for (int x = 0; x < n; x++) {
            if (x == 0 || nums[x] != nums[x - 1]) {
                while (y < n && (nums[y] < nums[x] + k || y <= x)) {
                    y++;
                }
                if (y < n && nums[y] == nums[x] + k) {
                    res++;
                }
            }
        }
        return res;
    }
}
```

```python
class Solution:
    def findPairs(self, nums: List[int], k: int) -> int:
        nums.sort()
        n, y, res = len(nums), 0, 0
        for x in range(n):
            if x == 0 or nums[x] != nums[x - 1]:
                while y < n and (nums[y] < nums[x] + k or y <= x):
                    y += 1
                if y < n and nums[y] == nums[x] + k:
                    res += 1
        return res
```

#### 思路

题干的两个条件可以这样解读：

- 数对的两个元素的下标值不同。当 k = 0 时，数对的两个元素值可以相同，但下标值必须不同。
- 数对的两个元素差值为 k。

这样的解读之下，原来 i 和 j 的大小关系被抹除了，只要求 i 和 j 不相等。而差值为 k 这一要求则可以在排序后使用双指针来满足。

将原数组升序排序，并用新的指针 x 和 y 来搜索数对。即寻找不同的 $(\textit{nums}[x], \textit{nums}[y])$ 满足：

- x < y
- $\textit{nums}[x] + k = \textit{nums}[y]$

记录满足要求的 x 的个数并返回。

#### 复杂度分析

- 时间复杂度：$O(n\log n)$，其中 n 是数组 $\text{nums}$的长度。排序需要消耗 $O(n\log n) $复杂度，遍历指针 x 消耗 O(n) 复杂度，指针 y 的值最多变化 O(n) 次，总的时间复杂度为 $O(n\log n)$。

- 空间复杂度：$O(\log n)$，其中 n 是数组 $\text{nums}$的长度。排序消耗 $O(\log n)$ 复杂度，其余消耗常数空间复杂度，总的空间复杂度为 $O(\log n)$。


