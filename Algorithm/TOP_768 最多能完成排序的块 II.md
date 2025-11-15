# 题目

*这个问题和“最多能完成排序的块”相似，但给定数组中的元素可以重复，输入数组最大长度为`2000`，其中的元素最大为`10**8`。*

`arr`是一个可能包含**重复元素**的整数数组，我们将这个数组分割成几个“块”，并将这些块分别进行排序。之后再连接起来，使得连接的结果和按升序排序后的原数组相同。

我们最多能将数组分成多少块？

**示例 1:**

```
输入: arr = [5,4,3,2,1]
输出: 1
解释:
将数组分成2块或者更多块，都无法得到所需的结果。
例如，分成 [5, 4], [3, 2, 1] 的结果是 [4, 5, 1, 2, 3]，这不是有序的数组。 
```

**示例 2:**

```
输入: arr = [2,1,3,4,4]
输出: 4
解释:
我们可以把它分成两块，例如 [2, 1], [3, 4, 4]。
然而，分成 [2, 1], [3], [4], [4] 可以得到最多的块数。 
```

**注意:**

- `arr`的长度在`[1, 2000]`之间。
- `arr[i]`的大小在`[0, 10**8]`之间。

## 我的解法

查看评论区大佬提示后写出！

```python
class Solution:
    def maxChunksToSorted(self, arr: List[int]) -> int:
        newArr = sorted(arr)
        ans = 0
        pref = 0
        for i in range(len(arr)):
            pref += arr[i] - newArr[i]
            if pref == 0:
                ans += 1
        return ans
```

## 其他解法

### 排序 + 哈希表

```c++
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        unordered_map<int, int> cnt;
        int res = 0;
        vector<int> sortedArr = arr;
        sort(sortedArr.begin(), sortedArr.end());
        for (int i = 0; i < sortedArr.size(); i++) {
            int x = arr[i], y = sortedArr[i];
            cnt[x]++;
            if (cnt[x] == 0) {
                cnt.erase(x);
            }
            cnt[y]--;
            if (cnt[y] == 0) {
                cnt.erase(y);
            }
            if (cnt.size() == 0) {
                res++;
            }
        }
        return res;
    }
};
```

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        Map<Integer, Integer> cnt = new HashMap<Integer, Integer>();
        int res = 0;
        int[] sortedArr = new int[arr.length];
        System.arraycopy(arr, 0, sortedArr, 0, arr.length);
        Arrays.sort(sortedArr);
        for (int i = 0; i < sortedArr.length; i++) {
            int x = arr[i], y = sortedArr[i];
            cnt.put(x, cnt.getOrDefault(x, 0) + 1);
            if (cnt.get(x) == 0) {
                cnt.remove(x);
            }
            cnt.put(y, cnt.getOrDefault(y, 0) - 1);
            if (cnt.get(y) == 0) {
                cnt.remove(y);
            }
            if (cnt.isEmpty()) {
                res++;
            }
        }
        return res;
    }
}
```

```python
class Solution:
    def maxChunksToSorted(self, arr: List[int]) -> int:
        cnt = Counter()
        res = 0
        for x, y in zip(arr, sorted(arr)):
            cnt[x] += 1
            if cnt[x] == 0:
                del cnt[x]
            cnt[y] -= 1
            if cnt[y] == 0:
                del cnt[y]
            if len(cnt) == 0:
                res += 1
        return res
```

#### 思路

记数组 $\textit{arr} $长度为 n，排完序的数组为 $\textit{sortedArr}$。首先，将原数组分为一块，肯定是可行的。原数组直接排序，和将它分为一块后再排序，得到的数组是相同的。那么，如何判断一个数组是否能分为符合题意的两块呢？如果一个数组能分为两块，那么一定能找到一个下标 k，这个下标将数组分为两个非空子数组 $arr[0, \ldots, k]$ 和 $arr[k+1, \ldots, n-1]$，使得 $arr[0, \ldots, k]$ 和 $sortedArr[0, \ldots, k]$ 的元素频次相同，$arr[k+1, \ldots, n-1]$ 和 $sortedArr[k+1, \ldots, n-1]$ 的元素频次相同。判断能否分为更多的块时同理。这个判断过程可以从左至右同时遍历 $\textit{arr}$ 和 $\textit{sortedArr}$，并用一个哈希表 $\textit{cnt}$ 来记录两个数组元素频次之差。当遍历到某个下标时，如果 $\textit{cnt}$ 内所有键的值均为 0，则表示划分出了一个新的块，最后记录有多少下标可以使得 $\textit{cnt}$内所有键的值均为 0 即可。

#### 复杂度分析

- 时间复杂度：$O(n \log n)$，其中 n 是输入数组$ \textit{arr}$ 的长度。排序需要消耗 $O(n \log n)$ 的时间复杂度，遍历一遍消耗 O(n) 的时间复杂度。

- 空间复杂度：O(n)。排序完的数组和哈希表均需要消耗 O(n) 的空间复杂度。


### 单调栈

```c++
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        stack<int> st;
        for (auto &num : arr) {
            if (st.empty() || num >= st.top()) {
                st.emplace(num);
            } else {
                int mx = st.top();
                st.pop();
                while (!st.empty() && st.top() > num) {
                    st.pop();
                }
                st.emplace(mx);
            }
        }
        return st.size();
    }
};
```

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        Deque<Integer> stack = new ArrayDeque<Integer>();
        for (int num : arr) {
            if (stack.isEmpty() || num >= stack.peek()) {
                stack.push(num);
            } else {
                int mx = stack.pop();
                while (!stack.isEmpty() && stack.peek() > num) {
                    stack.pop();
                }
                stack.push(mx);
            }
        }
        return stack.size();
    }
}
```

```python
class Solution:
    def maxChunksToSorted(self, arr: [int]) -> int:
        stack = []
        for a in arr:
            if len(stack) == 0 or a >= stack[-1]:
                stack.append(a)
            else:
                mx = stack.pop()
                while stack and stack[-1] > a:
                    stack.pop()
                stack.append(mx)
        return len(stack)
```

#### 思路

对于已经分好块的数组，若块数大于 1，则可以得到以下结论：右边的块的所有数字均大于或等于左边的块的所有数字。考虑这个问题：对于已经分好块的数组，若在其末尾添加一个数字，如何求得新数组的分块方式？

新添加的数字可能会改变原数组的分块方式。如果新添加的数字大于或等于原数组最后一个块的最大值，则这个新添加的数字可以自己形成一个块。如果新添加的数字小于原数组最后一个块的最大值，则它必须融入最后一个块。如果它大于或等于原数组倒数第二个块（如果有）的最大值，那么这个过程可以停止，新数组的分块方式已经求得。否则，它将继续融合原数组倒数第二个块，直到遇到一个块，使得该块的最大值小于或等于这个新添加的数，或者这个数字已经融合了所有块。

上述分析过程中，我们只用到了块的最大值来进行比较，比较过程又是从右到左，符合栈的思想，因此可以用类似单调栈的数据结构来存储块的最大值。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是输入数组 $\textit{arr}$的长度。需要遍历一遍数组，入栈的操作最多为 n 次。

- 空间复杂度：O(n)。栈的长度最多为 n。


