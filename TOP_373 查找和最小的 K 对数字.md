# 题目

给定两个以 **升序排列** 的整数数组 `nums1` 和 `nums2` , 以及一个整数 `k` 。

定义一对值 `(u,v)`，其中第一个元素来自 `nums1`，第二个元素来自 `nums2` 。

请找到和最小的 `k` 个数对 `(u1,v1)`, ` (u2,v2)` ...  `(uk,vk)` 。

**示例 1:**

```
输入: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
输出: [1,2],[1,4],[1,6]
解释: 返回序列中的前 3 对数：
     [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```

**示例 2:**

```
输入: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
输出: [1,1],[1,1]
解释: 返回序列中的前 2 对数：
     [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```

**示例 3:**

```
输入: nums1 = [1,2], nums2 = [3], k = 3 
输出: [1,3],[2,3]
解释: 也可能序列中所有的数对都被返回:[1,3],[2,3]
```

**提示:**

- $1 <= nums1.length, nums2.length <= 10^5$
- $-10^9 <= nums1[i], nums2[i] <= 10^9$
- `nums1` 和 `nums2` 均为升序排列
- `1 <= k <= 1000`

## 我的解法

暴力破解！超时！

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        ans = []
        n = len(nums1)
        m = len(nums2)
        for i in range(n):
            for j in range(min(m, k)):
                ans.append([nums1[i], nums2[j]])
        ans = sorted(ans, key=lambda x: x[0] + x[1])
        return ans[:k]
```

## 其他解法

### 优先队列

```c++
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        auto cmp = [&nums1, &nums2](const pair<int, int> & a, const pair<int, int> & b) {
            return nums1[a.first] + nums2[a.second] > nums1[b.first] + nums2[b.second];
        };

        int m = nums1.size();
        int n = nums2.size();
        vector<vector<int>> ans;   
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> pq(cmp);
        for (int i = 0; i < min(k, m); i++) {
            pq.emplace(i, 0);
        }
        while (k-- > 0 && !pq.empty()) {
            auto [x, y] = pq.top(); 
            pq.pop();
            ans.emplace_back(initializer_list<int>{nums1[x], nums2[y]});
            if (y + 1 < n) {
                pq.emplace(x, y + 1);
            }
        }

        return ans;
    }
};
```

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<int[]> pq = new PriorityQueue<>(k, (o1, o2)->{
            return nums1[o1[0]] + nums2[o1[1]] - nums1[o2[0]] - nums2[o2[1]];
        });
        List<List<Integer>> ans = new ArrayList<>();
        int m = nums1.length;
        int n = nums2.length;
        for (int i = 0; i < Math.min(m, k); i++) {
            pq.offer(new int[]{i,0});
        }
        while (k-- > 0 && !pq.isEmpty()) {
            int[] idxPair = pq.poll();
            List<Integer> list = new ArrayList<>();
            list.add(nums1[idxPair[0]]);
            list.add(nums2[idxPair[1]]);
            ans.add(list);
            if (idxPair[1] + 1 < n) {
                pq.offer(new int[]{idxPair[0], idxPair[1] + 1});
            }
        }
        
        return ans;
    }
}
```

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        m, n = len(nums1), len(nums2)
        ans = []
        pq = [(nums1[i] + nums2[0], i, 0) for i in range(min(k, m))]
        while pq and len(ans) < k:
            _, i, j = heappop(pq)
            ans.append([nums1[i], nums2[j]])
            if j + 1 < n:
                heappush(pq, (nums1[i] + nums2[j + 1], i, j + 1))
        return ans
```

#### 思路

本题与 TOP_719. 找出第 k 小的距离对 相似，可以参考该题的解法。对于已经按升序排列的两个数组 $\textit{nums}_1$,$\textit{nums}_2$，长度分别为 $\textit{length}_1$,$\textit{length}_2$，我们可以知道和最小的数对一定为 $(\textit{nums}_1[0], \textit{nums}_2[0])$，和最大的数对一定为 $(\textit{nums}_1[\textit{length}_1-1], \textit{nums}_2[\textit{length}_2-1])$。本题要求找到最小的 k 个数对，最直接的办法是可以将所有的数对求出来，然后利用排序或者$ \texttt{TopK}$ 解法求出最小的 k 个数对即可。实际求解时可以不用求出所有的数对，只需从所有符合待选的数对中选出最小的即可，假设当前已选的前 n 小数对的索引分别为 $(a_1,b_1),(a_2,b_2),(a_3,b_3),\ldots,(a_n,b_n)$，由于两个数组都是按照升序进行排序的，则可以推出第 n+1 小的数对的索引选择范围为 $(a_1+1,b_1),(a_1,b_1+1),(a_2+1,b_2),(a_2,b_2+1),(a_3+1,b_3),(a_3,b_3+1),\ldots,(a_n+1,b_n),(a_n,b_n+1)$，假设我们利用堆的特性可以求出待选范围中最小数对的索引为 $(a_{i},b_{i})$，同时将新的待选的数对 $(a_{i}+1,b_{i}),(a_{i},b_{i}+1)$ 加入到堆中，直到我们选出 k 个数对即可。

如果我们每次都将已选的数对 $(a_{i},b_{i})$ 的待选索引 $(a_{i}+1,b_{i}),(a_{i},b_{i}+1)$ 加入到堆中则可能出现重复的问题，一般需要设置标记位解决去重的问题。我们可以将 $\textit{nums}_1$的前 k 个索引数对 $(0,0),(1,0),\ldots,(k-1,0)$ 加入到队列中，每次从队列中取出元素 (x,y) 时，我们只需要将 $\textit{nums}_2$的索引增加即可，这样避免了重复加入元素的问题。

#### 复杂度分析

- 时间复杂度：$O(k \log k)$，其中 k 是选择的数对的数目。优先队列中最多只保存 k 个元素，每次压入新的元素队列进行调整的时间复杂度为 $\log k$，入队操作一共有 2k 次, 一共需要从队列中弹出 k 个数据。

- 空间复杂度：O(k)。优先队列中最多只保存 k 个元素。


### 二分查找

```c++
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        int m = nums1.size();
        int n = nums2.size();
        auto count = [&](int target){
            long long cnt = 0;
            int start = 0;
            int end = n - 1;
            while (start < m && end >= 0) {
                if (nums1[start] + nums2[end] > target) {
                    end--;
                } else {
                    cnt += end + 1;
                    start++;
                }
            }
            return cnt;
        };

        /*二分查找第 k 小的数对和的大小*/
        int left = nums1[0] + nums2[0];
        int right = nums1.back() + nums2.back();
        int pairSum = right;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (count(mid) < k) {
                left = mid + 1;
            } else {
                pairSum = mid;
                right = mid - 1;
            }
        }

        vector<vector<int>> ans;
        int pos = n - 1;
        /*找到小于目标值 pairSum 的数对*/
        for (int i = 0; i < m; i++) {
            while (pos >= 0 && nums1[i] + nums2[pos] >= pairSum) {
                pos--;
            }
            for (int j = 0; j <= pos && k > 0; j++, k--) {
                ans.push_back({nums1[i], nums2[j]});
            }
        }
        /*找到等于目标值 pairSum 的数对*/
        pos = n - 1;
        for (int i = 0; i < m && k > 0; i++) {
            while (pos >= 0 && nums1[i] + nums2[pos] > pairSum) {
                pos--;
            }
            for (int j = i; k > 0 && j >= 0 && nums1[j] + nums2[pos] == pairSum; j--, k--) {
                ans.push_back({nums1[i], nums2[pos]});
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        int m = nums1.length;
        int n = nums2.length;

        /*二分查找第 k 小的数对和的大小*/
        int left = nums1[0] + nums2[0];
        int right = nums1[m - 1] + nums2[n - 1];
        int pairSum = right;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            long cnt = 0;
            int start = 0;
            int end = n - 1;
            while (start < m && end >= 0) {
                if (nums1[start] + nums2[end] > mid) {
                    end--;
                } else {
                    cnt += end + 1;
                    start++;
                }
            }
            if (cnt < k) {
                left = mid + 1;
            } else {
                pairSum = mid;
                right = mid - 1;
            }
        }

        List<List<Integer>> ans = new ArrayList<>();
        int pos = n - 1;
        /*找到小于目标值 pairSum 的数对*/
        for (int i = 0; i < m; i++) {
            while (pos >= 0 && nums1[i] + nums2[pos] >= pairSum) {
                pos--;
            }
            for (int j = 0; j <= pos && k > 0; j++, k--) {
                List<Integer> list = new ArrayList<>();
                list.add(nums1[i]);
                list.add(nums2[j]);
                ans.add(list);
            }
        }

        /*找到等于目标值 pairSum 的数对*/
        pos = n - 1;
        for (int i = 0; i < m && k > 0; i++) {
            while (pos >= 0 && nums1[i] + nums2[pos] > pairSum) {
                pos--;
            }
            for (int j = i; k > 0 && j >= 0 && nums1[j] + nums2[pos] == pairSum; j--, k--) {
                List<Integer> list = new ArrayList<>();
                list.add(nums1[j]);
                list.add(nums2[pos]);
                ans.add(list);
            }
        }
        return ans;
    }
}
```

```python
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        m, n = len(nums1), len(nums2)

        # 二分查找第 k 小的数对和
        left, right = nums1[0] + nums2[0], nums1[m - 1] + nums2[n - 1] + 1
        while left < right:
            mid = (left + right) // 2
            cnt = 0
            i, j = 0, n - 1
            while i < m and j >= 0:
                if nums1[i] + nums2[j] > mid:
                    j -= 1
                else:
                    cnt += j + 1
                    i += 1
            if cnt < k:
                left = mid + 1
            else:
                right = mid
        pairSum = left

        ans = []
        # 找数对和小于 pairSum 的数对
        i = n - 1
        for num1 in nums1:
            while i >= 0 and num1 + nums2[i] >= pairSum:
                i -= 1
            for j in range(i + 1):
                ans.append([num1, nums2[j]])
                if len(ans) == k:
                    return ans

        # 找数对和等于 pairSum 的数对
        i = n - 1
        for num1 in nums1:
            while i >= 0 and num1 + nums2[i] > pairSum:
                i -= 1
            j = i
            while j >= 0 and num1 + nums2[j] == pairSum:
                ans.append([num1, nums2[j]])
                if len(ans) == k:
                    return ans
                j -= 1
        return ans
```

#### 思路

参考 TOP_378. 有序矩阵中第 K 小的元素 的二分查找的解法，我们利用二分查找找到第 k 小的数对和为 $\textit{pairSum}$。利用滑动窗口即可计算出两个数组中满足数对和小于等于目标值 $\textit{target}$的数对有多少个，我们找到最小的$ \textit{target}$ 且满足小于等于它的数对数目刚好大于等于 k 即为目标值 $\textit{pairSum}$，然后在数组中找到最小的 k 个数对满足数对和小于等于 $\textit{pairSum}$。

由于题目中数组 $\textit{nums}_1,\textit{nums}_2$中的元素存在重复，因此我们不能简单的利用滑动窗口找到所有满足小于等于 $\textit{pairSum}$ 的数对。因为存在小于等于 $\textit{pairSum}$ 的数对和的数目大于 k，因此数对和等于 $\textit{pairSum}$ 的数对不一定就属于最小的 k 个数对。
首先利用滑动窗口找到所有小于 $\textit{pairSum}$ 的数对，假设数对和小于 $\textit{pairSum}$ 的数目为 x 个，然后再利用二分查找在数组中找到 k-x 个和等于 $\textit{pairSum}$ 的数对即可。

#### 复杂度分析

- 时间复杂度：$O(k + (m + n) \times \log(\textit{diff}(\textit{nums}_1) + \textit{diff}(\textit{nums}_2)))$，其中 m, n 表示数组 $\textit{nums}_1,\textit{nums}_2$的长度，$\textit{diff}(arr)$ 表示数组 arr 中最大元素与最小元素之差，$\textit{diff}(\textit{nums}_1) = \max(\textit{nums}_1) - \min(\textit{nums}_1), \textit{diff}(\textit{nums}_2) = \max(\textit{nums}_2) - \min(\textit{nums}_2))$。我们利用二分查找找到满足要求的数对和的时间复杂度为 $(m + n) \times \log(\textit{diff}(\textit{nums}_1) + \textit{diff}(\textit{nums}_2))$，我们利用滑动窗口找到小于等于目标值的 k 个数对的时间复杂度为 $O(2 \times (k + m + n))$，所以总的时间复杂度 $O(2 \times (k + m + n) + (m + n) \times \log(\textit{diff}(\textit{nums}_1) + \textit{diff}(\textit{nums}_2))) = k + (m + n) \times \log(\textit{diff}(\textit{nums}_1) + \textit{diff}(\textit{nums}_2))$。

- 空间复杂度：O(1)，除了函数返回值以外，不需要额外的存储空间。


