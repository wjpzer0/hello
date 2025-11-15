# 题目

给你一个正整数 `n` ，请你找出符合条件的最小整数，其由重新排列 `n` 中存在的每位数字组成，并且其值大于 `n` 。如果不存在这样的正整数，则返回 `-1` 。

**注意** ，返回的整数应当是一个 **32 位整数** ，如果存在满足题意的答案，但不是 **32 位整数** ，同样返回 `-1` 。

**示例 1：**

```
输入：n = 12
输出：21
```

**示例 2：**

```
输入：n = 21
输出：-1
```

 

**提示：**

- $1 <= n <= 2^{31} - 1$

## 我的解法

没有理解关键思路！

```python
class Solution:
    def nextGreaterElement(self, n: int) -> int:
        def findNexBig(newList):
            res = []
            start = newList[-1]
            newList.sort()
            if newList[-1] == start:
                return None
            point = bisect_right(newList, start)
            res.append(newList[point])
            for i in range(len(newList)):
                if i == point:
                    continue
                res.append(newList[i])
            return res

        nlist = []
        while n:
            mid = n % 10
            n //= 10
            nlist.append(mid)

        nlen = len(nlist)
        for i in range(1, nlen + 1):
            newList = nlist[:i]
            resList = findNexBig(newList)
            if resList:
                break
        if resList == None:
            return -1
        ansList = nlist[i:][::-1] + resList
        ans = 0

        for i in range(len(ansList)):
            ans = ans * 10 + ansList[i]
        return ans if ans < 2 ** 31 else -1
```

## 其他解法

### 下一个排列

```c++
class Solution {
public:
    int nextGreaterElement(int n) {
        auto nums = to_string(n);
        int i = (int) nums.length() - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i < 0) {
            return -1;
        }

        int j = nums.size() - 1;
        while (j >= 0 && nums[i] >= nums[j]) {
            j--;
        }
        swap(nums[i], nums[j]);
        reverse(nums.begin() + i + 1, nums.end());
        long ans = stol(nums);
        return ans > INT_MAX ? -1 : ans;
    }
};
```

```java
class Solution {
    public int nextGreaterElement(int n) {
        char[] nums = Integer.toString(n).toCharArray();
        int i = nums.length - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i < 0) {
            return -1;
        }

        int j = nums.length - 1;
        while (j >= 0 && nums[i] >= nums[j]) {
            j--;
        }
        swap(nums, i, j);
        reverse(nums, i + 1);
        long ans = Long.parseLong(new String(nums));
        return ans > Integer.MAX_VALUE ? -1 : (int) ans;
    }

    public void reverse(char[] nums, int begin) {
        int i = begin, j = nums.length - 1;
        while (i < j) {
            swap(nums, i, j);
            i++;
            j--;
        }
    }

    public void swap(char[] nums, int i, int j) {
        char temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

```python
class Solution:
    def nextGreaterElement(self, n: int) -> int:
        nums = list(str(n))
        i = len(nums) - 2
        while i >= 0 and nums[i] >= nums[i + 1]:
            i -= 1
        if i < 0:
            return -1

        j = len(nums) - 1
        while j >= 0 and nums[i] >= nums[j]:
            j -= 1
        nums[i], nums[j] = nums[j], nums[i]
        nums[i + 1:] = nums[i + 1:][::-1]
        ans = int(''.join(nums))
        return ans if ans < 2 ** 31 else -1
```

把 n 转换成字符串（字符数组），那么本题实际上是在求字符数组的 TOP_31. 下一个排列，当不存在下一个排列时返回 -1。

#### **复杂度分析**

- 时间复杂度：$O(\log n)$。
- 空间复杂度：$O(\log n)$。

### 数学

```c++
class Solution {
public:
    int nextGreaterElement(int n) {
        int x = n, cnt = 1;
        for (; x >= 10 && x / 10 % 10 >= x % 10; x /= 10) {
            ++cnt;
        }
        x /= 10;
        if (x == 0) {
            return -1;
        }

        int targetDigit = x % 10;
        int x2 = n, cnt2 = 0;
        for (; x2 % 10 <= targetDigit; x2 /= 10) {
            ++cnt2;
        }
        x += x2 % 10 - targetDigit; // 把 x2 % 10 换到 targetDigit 上

        for (int i = 0; i < cnt; ++i, n /= 10) { // 反转 n 末尾的 cnt 个数字拼到 x 后
            int d = i != cnt2 ? n % 10 : targetDigit;
            if (x > INT_MAX / 10 || x == INT_MAX / 10 && d > 7) {
                return -1;
            }
            x = x * 10 + d;
        }
        return x;
    }
};
```

```java
class Solution {
    public int nextGreaterElement(int n) {
        int x = n, cnt = 1;
        for (; x >= 10 && x / 10 % 10 >= x % 10; x /= 10) {
            ++cnt;
        }
        x /= 10;
        if (x == 0) {
            return -1;
        }

        int targetDigit = x % 10;
        int x2 = n, cnt2 = 0;
        for (; x2 % 10 <= targetDigit; x2 /= 10) {
            ++cnt2;
        }
        x += x2 % 10 - targetDigit; // 把 x2 % 10 换到 targetDigit 上

        for (int i = 0; i < cnt; ++i, n /= 10) { // 反转 n 末尾的 cnt 个数字拼到 x 后
            int d = i != cnt2 ? n % 10 : targetDigit;
            if (x > Integer.MAX_VALUE / 10 || x == Integer.MAX_VALUE / 10 && d > 7) {
                return -1;
            }
            x = x * 10 + d;
        }
        return x;
    }
}
```

```python
class Solution:
    def nextGreaterElement(self, n: int) -> int:
        x, cnt = n, 1
        while x >= 10 and x // 10 % 10 >= x % 10:
            cnt += 1
            x //= 10
        x //= 10
        if x == 0:
            return -1

        targetDigit = x % 10
        x2, cnt2 = n, 0
        while x2 % 10 <= targetDigit:
            cnt2 += 1
            x2 //= 10
        x += x2 % 10 - targetDigit  # 把 x2 % 10 换到 targetDigit 上

        MAX_INT = 2 ** 31 - 1
        for i in range(cnt):  # 反转 n 末尾的 cnt 个数字拼到 x 后
            d = n % 10 if i != cnt2 else targetDigit
            # 为了演示算法，请读者把 x 视作一个 32 位有符号整数
            if x > MAX_INT // 10 or x == MAX_INT // 10 and d > 7:
                return -1
            x = x * 10 + d
            n //= 10
        return x
```

不转换成字符数组，如何用 O(1) 空间复杂度解决此题？

如果还要求不使用 64 位整数呢？

类似方法一，我们从 n 开始，不断比较其最低位数字和次低位数字的大小，如果次低位数字不低于最低位数字，则移除最低位数字，继续循环。循环结束后的 n 就对应着方法一的下标 i，即 $\textit{nums}$的前 i+1 个字符。

对于方法一中下标 j 的计算也是同理。

最后，我们参考 7. 整数反转的官方题解 的做法，将 i+1 之后的部分反转，即得到下一个整数。如果中途计算会溢出，则返回 -1。

#### **复杂度分析**

- 时间复杂度：$O(\log n)$。
- 空间复杂度：O(1)。我们只需要常数的空间保存若干变量。