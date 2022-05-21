# 题目

给你一个整数数组 `nums` ，该数组具有以下属性：

- `nums.length == 2 * n`.
- `nums` 包含 `n + 1` 个 **不同的** 元素
- `nums` 中恰有一个元素重复 `n` 次

找出并返回重复了 `n` 次的那个元素。

**示例 1：**

```
输入：nums = [1,2,3,3]
输出：3
```

**示例 2：**

```
输入：nums = [2,1,2,5,3,2]
输出：2
```

**示例 3：**

```
输入：nums = [5,1,5,2,5,3,5,4]
输出：5
```

**提示：**

- `2 <= n <= 5000`
- `nums.length == 2 * n`
- $0 <= nums[i] <= 10^4$
- `nums` 由 `n + 1` 个 **不同的** 元素组成，且其中一个元素恰好重复 `n` 次

## 我的解法

一开始用字典，后面想到了摩尔投票法查看大佬代码后写出！

```python
class Solution:
    def repeatedNTimes(self, nums: List[int]) -> int:
        res = nums[0]
        n = len(nums)

        count = 1
        curr = nums[0]
        for i in range(1, n):
            if nums[i] == res:
                return res

            if curr == nums[i]:
                count += 1
            else:
                count -= 1

            if count <= 0:
                curr = nums[i]
                count = 1
                
            if count > 1:
                return curr
        return curr
```

## 其他解法

### 哈希表

```c++
class Solution {
public:
    int repeatedNTimes(vector<int>& nums) {
        unordered_set<int> found;
        for (int num: nums) {
            if (found.count(num)) {
                return num;
            }
            found.insert(num);
        }
        // 不可能的情况
        return -1;
    }
};
```

```java
class Solution {
    public int repeatedNTimes(int[] nums) {
        Set<Integer> found = new HashSet<Integer>();
        for (int num : nums) {
            if (!found.add(num)) {
                return num;
            }
        }
        // 不可能的情况
        return -1;
    }
}
```

```python
class Solution:
    def repeatedNTimes(self, nums: List[int]) -> int:
        found = set()

        for num in nums:
            if num in found:
                return num
            found.add(num)
        
        # 不可能的情况
        return -1
```

#### 思路与算法

记重复 n 次的元素为 x。由于数组 $\textit{nums}$中有 n+1 个不同的元素，而其长度为 2n，那么数组中剩余的元素均只出现了一次。也就是说，我们只需要找到重复出现的元素即为答案。

因此我们可以对数组进行一次遍历，并使用哈希集合存储已经出现过的元素。如果遍历到了哈希集合中的元素，那么返回该元素作为答案。

#### 复杂度分析

时间复杂度：O(n)。我们只需要对数组$ \textit{nums}$进行一次遍历。

空间复杂度：O(n)，即为哈希集合需要使用的空间。

### 数学

```c++
class Solution {
public:
    int repeatedNTimes(vector<int>& nums) {
        int n = nums.size();
        for (int gap = 1; gap <= 3; ++gap) {
            for (int i = 0; i + gap < n; ++i) {
                if (nums[i] == nums[i + gap]) {
                    return nums[i];
                }
            }
        }
        // 不可能的情况
        return -1;
    }
};
```

```java
class Solution {
    public int repeatedNTimes(int[] nums) {
        int n = nums.length;
        for (int gap = 1; gap <= 3; ++gap) {
            for (int i = 0; i + gap < n; ++i) {
                if (nums[i] == nums[i + gap]) {
                    return nums[i];
                }
            }
        }
        // 不可能的情况
        return -1;
    }
}
```

```python
class Solution:
    def repeatedNTimes(self, nums: List[int]) -> int:
        n = len(nums)
        for gap in range(1, 4):
            for i in range(n - gap):
                if nums[i] == nums[i + gap]:
                    return nums[i]
        
        # 不可能的情况
        return -1
```

#### 思路与算法

我们可以考虑重复的元素 x 在数组 $\textit{nums}$中出现的位置。

如果相邻的 x 之间至少都隔了 2 个位置，那么数组的总长度至少为：

$$
n + 2(n - 1) = 3n - 2
$$
当 n > 2 时，3n-2 > 2n，不存在满足要求的数组。因此一定存在两个相邻的 x，它们的位置是连续的，或者只隔了 1 个位置。

当 n = 2 时，数组的长度最多为 2n = 4，因此最多只能隔 2 个位置。

这样一来，我们只需要遍历所有间隔 2 个位置及以内的下标对，判断对应的元素是否相等即可。

#### **复杂度分析**

- 时间复杂度：O(n)。我们最多对数组进行三次遍历（除了 n=2 之外，最多两次遍历）。
- 空间复杂度：O(1)。

### 随机选择

```c++
class Solution {
public:
    int repeatedNTimes(vector<int>& nums) {
        int n = nums.size();
        mt19937 gen{random_device{}()};
        uniform_int_distribution<int> dis(0, n - 1);

        while (true) {
            int x = dis(gen), y = dis(gen);
            if (x != y && nums[x] == nums[y]) {
                return nums[x];
            }
        }
    }
};
```

```java
class Solution {
    public int repeatedNTimes(int[] nums) {
        int n = nums.length;
        Random random = new Random();

        while (true) {
            int x = random.nextInt(n), y = random.nextInt(n);
            if (x != y && nums[x] == nums[y]) {
                return nums[x];
            }
        }
    }
}
```

```python
class Solution:
    def repeatedNTimes(self, nums: List[int]) -> int:
        n = len(nums)

        while True:
            x, y = random.randrange(n), random.randrange(n)
            if x != y and nums[x] == nums[y]:
                return nums[x]
```

#### **思路与算法**

我们可以每次随机选择两个不同的下标，判断它们对应的元素是否相等即可。如果相等，那么返回任意一个作为答案。

#### 复杂度分析

- 时间复杂度：期望 O(1)。选择两个相同元素的概率为$ \dfrac{n}{2n} \times \dfrac{n-1}{2n} \approx \dfrac{1}{4} $，因此期望 4 次结束循环。
- 空间复杂度：O(1)。


