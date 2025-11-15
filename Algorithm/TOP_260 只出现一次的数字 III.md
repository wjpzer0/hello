# 题目

给定一个整数数组 `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 **任意顺序** 返回答案。

**进阶：**你的算法应该具有线性时间复杂度。你能否仅使用常数空间复杂度来实现？

**示例 1：**

```
输入：nums = [1,2,1,3,2,5]
输出：[3,5]
解释：[5, 3] 也是有效的答案。
```

**示例 2：**

```
输入：nums = [-1,0]
输出：[-1,0]
```

**示例 3：**

```
输入：nums = [0,1]
输出：[1,0]
```

**提示：**

- $2 <= nums.length <= 3 * 10^4$
- $-2^{31} <= nums[i] <= 2^{31} - 1$
- 除两个只出现一次的整数外，`nums` 中的其他数字都出现两次

## 我的解法

想到进阶解法的异或，无法分开两个数的异或值！

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> List[int]:
        ans = []
        dic = {}
        for i in nums:
            if i not in dic:
                dic[i] = 1
            else:
                dic[i] += 1
        
        for num, step in dic.items():
            if step == 1:
                ans.append(num)
        return ans
```

## 其他解法

### 哈希表

```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        unordered_map<int, int> freq;
        for (int num: nums) {
            ++freq[num];
        }
        vector<int> ans;
        for (const auto& [num, occ]: freq) {
            if (occ == 1) {
                ans.push_back(num);
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<Integer, Integer>();
        for (int num : nums) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }
        int[] ans = new int[2];
        int index = 0;
        for (Map.Entry<Integer, Integer> entry : freq.entrySet()) {
            if (entry.getValue() == 1) {
                ans[index++] = entry.getKey();
            }
        }
        return ans;
    }
}
```

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> List[int]:
        freq = Counter(nums)
        return [num for num, occ in freq.items() if occ == 1]
```

#### 思路与算法

我们可以使用一个哈希映射统计数组中每一个元素出现的次数。

在统计完成后，我们对哈希映射进行遍历，将所有只出现了一次的数放入答案中。

### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组 $\textit{nums}$ 的长度。

- 空间复杂度：O(n)，即为哈希映射需要使用的空间。


### 位运算

```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int xorsum = 0;
        for (int num: nums) {
            xorsum ^= num;
        }
        // 防止溢出
        int lsb = (xorsum == INT_MIN ? xorsum : xorsum & (-xorsum));
        int type1 = 0, type2 = 0;
        for (int num: nums) {
            if (num & lsb) {
                type1 ^= num;
            }
            else {
                type2 ^= num;
            }
        }
        return {type1, type2};
    }
};
```

```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int xorsum = 0;
        for (int num : nums) {
            xorsum ^= num;
        }
        // 防止溢出
        int lsb = (xorsum == Integer.MIN_VALUE ? xorsum : xorsum & (-xorsum));
        int type1 = 0, type2 = 0;
        for (int num : nums) {
            if ((num & lsb) != 0) {
                type1 ^= num;
            } else {
                type2 ^= num;
            }
        }
        return new int[]{type1, type2};
    }
}
```

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> List[int]:
        xorsum = 0
        for num in nums:
            xorsum ^= num
        
        lsb = xorsum & (-xorsum)
        type1 = type2 = 0
        for num in nums:
            if num & lsb:
                type1 ^= num
            else:
                type2 ^= num
        
        return [type1, type2]
```

#### 思路与算法

在理解如何使用位运算解决本题前，读者需要首先掌握 TOP_136. 只出现一次的数字 中的位运算做法。

假设数组 $\textit{nums}$中只出现一次的元素分别是$ x_1$和 $x_2$。如果把 $\textit{nums}$中的所有元素全部异或起来，得到结果 x，那么一定有：

$$
x = x_1 \oplus x_2
$$
其中 $\oplus$ 表示异或运算。这是因为 $\textit{nums}$中出现两次的元素都会因为异或运算的性质 $a \oplus b \oplus b = a$ 抵消掉，那么最终的结果就只剩下 $x_1$和 $x_2$的异或和。x 显然不会等于 0，因为如果 x=0，那么说明 $x_1 = x_2$，这样 $x_1$和 $x_2$就不是只出现一次的数字了。因此，我们可以使用位运算 $\texttt{x \& -x}$ 取出 x 的二进制表示中最低位那个 1，设其为第 l 位，那么 $x_1$和 $x_2$中的某一个数的二进制表示的第 l 位为 0，另一个数的二进制表示的第 l 位为 1。在这种情况下，$x_1 \oplus x_2$的二进制表示的第 l 位才能为 1。

这样一来，我们就可以把$ \textit{nums}$中的所有元素分成两类，其中一类包含所有二进制表示的第 ll 位为 00 的数，另一类包含所有二进制表示的第 l 位为 1 的数。可以发现：

- 对于任意一个在数组 $\textit{nums}$ 中出现两次的元素，该元素的两次出现会被包含在同一类中；

- 对于任意一个在数组 $\textit{nums}$中只出现了一次的元素，即 $x_1$和 $x_2$，它们会被包含在不同类中。

因此，如果我们将每一类的元素全部异或起来，那么其中一类会得到 $x_1$，另一类会得到 $x_2$。这样我们就找出了这两个只出现一次的元素。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n是数组 $\textit{nums}$ 的长度。
- 空间复杂度：O(1)。