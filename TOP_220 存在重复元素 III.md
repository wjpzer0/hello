# 题目

给你一个整数数组 nums 和两个整数 k 和 t 。请你判断是否存在 两个不同下标 i 和 j，使得 abs(nums[i] - nums[j]) <= t ，同时又满足 abs(i - j) <= k 。

如果存在则返回 true，不存在返回 false。

示例 1：

```
输入：nums = [1,2,3,1], k = 3, t = 0
输出：true
```


示例 2：

```
输入：nums = [1,0,1,1], k = 1, t = 2
输出：true
```


示例 3：

```
输入：nums = [1,5,9,1,5,9], k = 2, t = 3
输出：false
```


提示：

- 0 <= nums.length <= 2 * $10^4$
- $-2^{31}$ <= nums[i] <= $2^{31} - 1$
- 0 <= k <= $10^4$
- 0 <= t <= $2^{31} - 1$

## 我的解法

超时

```python
class Solution:
    def containsNearbyAlmostDuplicate(self, nums: List[int], k: int, t: int) -> bool:
        i = 0
        j = i + k
        while i < len(nums) - 1:
            for differ in range(1, k + 1):
                if i + differ >= len(nums):
                    break
                if abs(nums[i] - nums[i + differ]) <= t:
                    return True
            i += 1
            j += 1
        return False
```

暴力破解！

## 其他解法

### 滑动窗口 + 有序集合

```java
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        int n = nums.length;
        TreeSet<Long> set = new TreeSet<Long>();
        for (int i = 0; i < n; i++) {
            Long ceiling = set.ceiling((long) nums[i] - (long) t);
            if (ceiling != null && ceiling <= (long) nums[i] + (long) t) {
                return true;
            }
            set.add((long) nums[i]);
            if (i >= k) {
                set.remove((long) nums[i - k]);
            }
        }
        return false;
    }
}
```

```c++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        int n = nums.size();
        set<int> rec;
        for (int i = 0; i < n; i++) {
            auto iter = rec.lower_bound(max(nums[i], INT_MIN + t) - t);
            if (iter != rec.end() && *iter <= min(nums[i], INT_MAX - t) + t) {
                return true;
            }
            rec.insert(nums[i]);
            if (i >= k) {
                rec.erase(nums[i - k]);
            }
        }
        return false;
    }
};
```



#### 思路及算法

对于序列中每一个元素 x 左侧的至多 k 个元素，如果这 k 个元素中存在一个元素落在区间 [x - t, x + t] 中，我们就找到了一对符合条件的元素。注意到对于两个相邻的元素，它们各自的左侧的 k 个元素中有 k - 1 个是重合的。于是我们可以使用滑动窗口的思路，维护一个大小为 k 的滑动窗口，每次遍历到元素 x 时，滑动窗口中包含元素 x 前面的最多 k 个元素，我们检查窗口中是否存在元素落在区间 [x−t,x+t] 中即可。

如果使用队列维护滑动窗口内的元素，由于元素是无序的，我们只能对于每个元素都遍历一次队列来检查是否有元素符合条件。如果数组的长度为 n，则使用队列的时间复杂度为 O(nk)，会超出时间限制。

因此我们希望能够找到一个数据结构维护滑动窗口内的元素，该数据结构需要满足以下操作：

- 支持添加和删除指定元素的操作，否则我们无法维护滑动窗口；

- 内部元素有序，支持二分查找的操作，这样我们可以快速判断滑动窗口中是否存在元素满足条件，具体而言，对于元素 x，当我们希望判断滑动窗口中是否存在某个数 yy 落在区间 [x - t, x + t] 中，只需要判断滑动窗口中所有大于等于 x - t 的元素中的最小元素是否小于等于 x + t 即可。


我们可以使用有序集合来支持这些操作。

实现方面，我们在有序集合中查找大于等于 x - t 的最小的元素 y，如果 y 存在，且 y≤x+t，我们就找到了一对符合条件的元素。完成检查后，我们将 x 插入到有序集合中，如果有序集合中元素数量超过了 k，我们将有序集合中最早被插入的元素删除即可。

**注意**

如果当前有序集合中存在相同元素，那么此时程序将直接返回 true。因此本题中的有序集合无需处理相同元素的情况。

为防止整型 int 溢出，我们既可以使用长整型 long，也可以对查找区间 [x−t,x+t] 进行限制，使其落在 int 范围内。

#### 复杂度分析

- 时间复杂度：O(nlog(min(n,k)))，其中 n 是给定数组的长度。每个元素至多被插入有序集合和从有序集合中删除一次，每次操作时间复杂度均为 O(log(min(n,k))。

- 空间复杂度：O(min(n,k))，其中 n 是给定数组的长度。有序集合中至多包含 min(n,k+1) 个元素。


### 桶

```java
class Solution {
public:
    int getID(int x, long w) {
        return x < 0 ? (x + 1ll) / w - 1 : x / w;
    }

    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        unordered_map<int, int> mp;
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            long x = nums[i];
            int id = getID(x, t + 1ll);
            if (mp.count(id)) {
                return true;
            }
            if (mp.count(id - 1) && abs(x - mp[id - 1]) <= t) {
                return true;
            }
            if (mp.count(id + 1) && abs(x - mp[id + 1]) <= t) {
                return true;
            }
            mp[id] = x;
            if (i >= k) {
                mp.erase(getID(nums[i - k], t + 1ll));
            }
        }
        return false;
    }
};
```

```c++
class Solution {
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        int n = nums.length;
        Map<Long, Long> map = new HashMap<Long, Long>();
        long w = (long) t + 1;
        for (int i = 0; i < n; i++) {
            long id = getID(nums[i], w);
            if (map.containsKey(id)) {
                return true;
            }
            if (map.containsKey(id - 1) && Math.abs(nums[i] - map.get(id - 1)) < w) {
                return true;
            }
            if (map.containsKey(id + 1) && Math.abs(nums[i] - map.get(id + 1)) < w) {
                return true;
            }
            map.put(id, (long) nums[i]);
            if (i >= k) {
                map.remove(getID(nums[i - k], w));
            }
        }
        return false;
    }

    public long getID(long x, long w) {
        if (x >= 0) {
            return x / w;
        }
        return (x + 1) / w - 1;
    }
}
```

#### 思路及算法

我们也可以使用利用桶排序的思想解决本题。我们按照元素的大小进行分桶，维护一个滑动窗口内的元素对应的元素。

对于元素 x，其影响的区间为[x−t,x+t]。于是我们可以设定桶的大小为 t + 1。如果两个元素同属一个桶，那么这两个元素必然符合条件。如果两个元素属于相邻桶，那么我们需要校验这两个元素是否差值不超过 tt。如果两个元素既不属于同一个桶，也不属于相邻桶，那么这两个元素必然不符合条件。

具体地，我们遍历该序列，假设当前遍历到元素 x，那么我们首先检查 x 所属于的桶是否已经存在元素，如果存在，那么我们就找到了一对符合条件的元素，否则我们继续检查两个相邻的桶内是否存在符合条件的元素。

实现方面，我们将 int 范围内的每一个整数 x 表示为 x=(t+1)×a+b(0≤b≤t) 的形式，这样 x 即归属于编号为 a 的桶。因为一个桶内至多只会有一个元素，所以我们使用哈希表实现即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是给定数组的长度。每个元素至多被插入哈希表和从哈希表中删除一次，每次操作的时间复杂度均为 O(1)。

- 空间复杂度：O(min(n,k))，其中 nn 是给定数组的长度。哈希表中至多包含 min(n,k+1) 个元素。


