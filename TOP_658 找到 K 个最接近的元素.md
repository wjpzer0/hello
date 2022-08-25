# 题目

给定一个 **排序好** 的数组 `arr` ，两个整数 `k` 和 `x` ，从数组中找到最靠近 `x`（两数之差最小）的 `k` 个数。返回的结果必须要是按升序排好的。

整数 `a` 比整数 `b` 更接近 `x` 需要满足：

- `|a - x| < |b - x|` 或者
- `|a - x| == |b - x|` 且 `a < b`

**示例 1：**

```
输入：arr = [1,2,3,4,5], k = 4, x = 3
输出：[1,2,3,4]
```

**示例 2：**

```
输入：arr = [1,2,3,4,5], k = 4, x = -1
输出：[1,2,3,4]
```

**提示：**

- `1 <= k <= arr.length`
- $1 <= arr.length <= 10^4$
- `arr` 按 **升序** 排列
- $-10^4 <= arr[i], x <= 10^4$

## 我的解法

憨批解法！运用所以题目条件！

```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        diffK = defaultdict(list)
        for num in arr:
            mid = abs(num - x)
            diffK[mid].append(num)
        newDiff = list(diffK.keys())
        newDiff.sort()
        ans = []
        for num in newDiff:
            mid = diffK[num]
            if len(mid) + len(ans) <= k:
                ans += mid
            else:
                diff = k - len(ans)
                ans += mid[:diff]
        ans.sort(reverse=False) 
        return ans
```

## 其它解法

### 排序

```c++
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        sort(arr.begin(), arr.end(), [x](int a, int b) -> bool {
            return abs(a - x) < abs(b - x) || abs(a - x) == abs(b - x) && a < b;
        });
        sort(arr.begin(), arr.begin() + k);
        return vector<int>(arr.begin(), arr.begin() + k);
    }
};
```

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        List<Integer> list = new ArrayList<Integer>();
        for (int num : arr) {
            list.add(num);
        }
        Collections.sort(list, (a, b) -> {
            if (Math.abs(a - x) != Math.abs(b - x)) {
                return Math.abs(a - x) - Math.abs(b - x);
            } else {
                return a - b;
            }
        });
        List<Integer> ans = list.subList(0, k);
        Collections.sort(ans);
        return ans;
    }
}
```

```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        arr.sort(key=lambda v: abs(v - x))
        return sorted(arr[:k])
```

首先将数组 $\textit{arr}$按照「更接近」的定义进行排序，如果 a 比 b 更接近 x，那么 a 将排在 b 前面。排序完成之后，k 个最接近的元素就是数组 $\textit{arr}$的前 k 个元素，将这 k 个元素从小到大进行排序后，直接返回。

#### 复杂度分析

- 时间复杂度：$O(n \log n)$，其中 n 是数组 $\textit{arr}$的长度。排序需要 $O(n \log n)$。

- 空间复杂度：$O(\log n)$。返回值不计算时间复杂度。排序需要 $O(\log n)$ 的栈空间。


### 二分查找 + 双指针

```c++
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int right = lower_bound(arr.begin(), arr.end(), x) - arr.begin();
        int left = right - 1;
        while (k--) {
            if (left < 0) {
                right++;
            } else if (right >= arr.size()) {
                left--;
            } else if (x - arr[left] <= arr[right] - x) {
                left--;
            } else {
                right++;
            }
        }
        return vector<int>(arr.begin() + left + 1, arr.begin() + right);
    }
};
```

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int right = binarySearch(arr, x);
        int left = right - 1;
        while (k-- > 0) {
            if (left < 0) {
                right++;
            } else if (right >= arr.length) {
                left--;
            } else if (x - arr[left] <= arr[right] - x) {
                left--;
            } else {
                right++;
            }
        }
        List<Integer> ans = new ArrayList<Integer>();
        for (int i = left + 1; i < right; i++) {
            ans.add(arr[i]);
        }
        return ans;
    }

    public int binarySearch(int[] arr, int x) {
        int low = 0, high = arr.length - 1;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (arr[mid] >= x) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
}
```

```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        right = bisect_left(arr, x)
        left = right - 1
        for _ in range(k):
            if left < 0:
                right += 1
            elif right >= len(arr) or x - arr[left] <= arr[right] - x:
                left -= 1
            else:
                right += 1
        return arr[left + 1: right]
```

假设数组长度为 nn，注意到数组 $\textit{arr}$已经按照升序排序，我们可以将数组 $\textit{arr}$分成两部分，前一部分所有元素 $[0, \textit{left}]$ 都小于 x，后一部分所有元素 $[\textit{right}, n - 1]$ 都大于等于 x，$\textit{left}$与 $\textit{right}$都可以通过二分查找获得。

$\textit{left}$和 $\textit{right}$指向的元素都是各自部分最接近 x 的元素，因此我们可以通过比较 $\textit{left}$和 $\textit{right}$指向的元素获取整体最接近 x 的元素。如果 $x - \textit{arr}[\textit{left}] \le \textit{arr}[\textit{right}] - x$，那么将$ \textit{left}$减一，否则将 $\textit{right}$加一。相应地，如果 $\textit{left}$或 $\textit{right}$已经越界，那么不考虑对应部分的元素。

最后，区间$ [\textit{left} + 1, \textit{right} - 1]$ 的元素就是我们所要获得的结果，返回答案既可。

#### 复杂度分析

- 时间复杂度：$O(\log n + k)$，其中 n 是数组 $\textit{arr}$的长度。二分查找需要 $O(\log n)$，双指针查找需要 O(k)。

- 空间复杂度：O(1)。返回值不计入空间复杂度。


