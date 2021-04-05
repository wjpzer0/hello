# 题目

给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。

说明：
初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。
你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。

示例 1：

```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]
```


示例 2：

```
输入：nums1 = [1], m = 1, nums2 = [], n = 0
输出：[1]
```



提示：

- nums1.length == m + n
- nums2.length == n
- 0 <= m, n <= 200
- 1 <= m + n <= 200
- $-10^9 <= nums1[i], nums2[i] <= 10^9$

## 我的解法

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        
        nums = nums1[:m] + nums2
        # nums.sort()
        print(nums)
        num = 0
        for i in nums:
                nums1[num] = i
                num += 1
```

最憨的解法

## 其他解法

### 合并后排序

```python
class Solution(object):
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        nums1[:] = sorted(nums1[:m] + nums2)
```

最朴素的解法就是将两个数组合并之后再排序。该算法只需要一行(Java是2行)，时间复杂度较差，为O((n+m)log(n+m))。这是由于这种方法没有利用两个数组本身已经有序这一点。

#### **复杂度分析**

- 时间复杂度 : O*((*n*+*m*)log(*n*+*m*))。
- 空间复杂度 : O(1)。

###  双指针 / 从前往后

```python
class Solution(object):
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        # Make a copy of nums1.
        nums1_copy = nums1[:m] 
        nums1[:] = []

        # Two get pointers for nums1_copy and nums2.
        p1 = 0 
        p2 = 0
        
        # Compare elements from nums1_copy and nums2
        # and add the smallest one into nums1.
        while p1 < m and p2 < n: 
            if nums1_copy[p1] < nums2[p2]: 
                nums1.append(nums1_copy[p1])
                p1 += 1
            else:
                nums1.append(nums2[p2])
                p2 += 1

        # if there are still elements to add
        if p1 < m: 
            nums1[p1 + p2:] = nums1_copy[p1:]
        if p2 < n:
            nums1[p1 + p2:] = nums2[p2:]
```

一般而言，对于有序数组可以通过 双指针法 达到O(n + m)O(n+m)的时间复杂度。
最直接的算法实现是将指针p1 置为 nums1的开头， p2为 nums2的开头，在每一步将最小值放入输出数组中。
由于 nums1 是用于输出的数组，需要将nums1中的前m个元素放在其他地方，也就需要 O(m)O(m) 的空间复杂度。

![image.png](https://pic.leetcode-cn.com/992f95361c37ad06deadb6f14a9970d0184fd47330365400dd1d6f7be239e0ff-image.png)

#### **复杂度分析**

- 时间复杂度 : O(n + m)。
- 空间复杂度 : O(m)。

###  双指针 / 从后往前

```python
class Solution(object):
    def merge(self, nums1, m, nums2, n):
        """
        :type nums1: List[int]
        :type m: int
        :type nums2: List[int]
        :type n: int
        :rtype: void Do not return anything, modify nums1 in-place instead.
        """
        # two get pointers for nums1 and nums2
        p1 = m - 1
        p2 = n - 1
        # set pointer for nums1
        p = m + n - 1
        
        # while there are still elements to compare
        while p1 >= 0 and p2 >= 0:
            if nums1[p1] < nums2[p2]:
                nums1[p] = nums2[p2]
                p2 -= 1
            else:
                nums1[p] =  nums1[p1]
                p1 -= 1
            p -= 1
        
        # add missing elements from nums2
        nums1[:p2 + 1] = nums2[:p2 + 1]
```

![img](https://pic.leetcode-cn.com/c1ab224d0cf26c76320168efde66951bedd2d02ae89b8942e97121acf04fa36b-image.png)

方法二已经取得了最优的时间复杂度O(n + m)，但需要使用额外空间。这是由于在从头改变nums1的值时，需要把nums1中的元素存放在其他位置。
如果我们从结尾开始改写 nums1 的值又会如何呢？这里没有信息，因此不需要额外空间。
这里的指针 p 用于追踪添加元素的位置

#### **复杂度分析**

- 时间复杂度 : O(n + m)。
- 空间复杂度 : O(1)。

# 第二次解题

**第二次解法与第一次解法相同，仍然没有认真思考新的解法。**

## 其他解法

### 直接合并后排序

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        nums1[m:] = nums2
        nums1.sort()
```

#### 算法

最直观的方法是先将数组 $\textit{nums}_2$放进数组 $\textit{nums}_1$的尾部，然后直接对整个数组进行排序。

#### 复杂度分析

- 时间复杂度：O((m+n)log(m+n))。
  排序序列长度为 m+n，套用快速排序的时间复杂度即可，平均情况为 O((m+n)log(m+n))。
- 空间复杂度：O(log(m+n))。
  排序序列长度为 m+n，套用快速排序的空间复杂度即可，平均情况为 O(log(m+n))。

### 双指针

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        sorted = []
        p1, p2 = 0, 0
        while p1 < m or p2 < n:
            if p1 == m:
                sorted.append(nums2[p2])
                p2 += 1
            elif p2 == n:
                sorted.append(nums1[p1])
                p1 += 1
            elif nums1[p1] < nums2[p2]:
                sorted.append(nums1[p1])
                p1 += 1
            else:
                sorted.append(nums2[p2])
                p2 += 1
        nums1[:] = sorted
```



#### 算法

方法一没有利用数组 $\textit{nums}_1$与 $\textit{nums}_2$已经被排序的性质。为了利用这一性质，我们可以使用双指针方法。这一方法将两个数组看作队列，每次从两个数组头部取出比较小的数字放到结果中。如下面的动画所示：

![gif1](https://assets.leetcode-cn.com/solution-static/88/1.gif)

我们为两个数组分别设置一个指针 $p_1$ 与 $p_2$ 来作为队列的头部指针。

#### 复杂度分析

- 时间复杂度：O(m+n)。
  指针移动单调递增，最多移动 m+n 次，因此时间复杂度为 O(m+n)。
- 空间复杂度：O(m+n)。
  需要建立长度为 m+n 的中间数组 sorted。

### 逆向双指针

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        p1, p2 = m - 1, n - 1
        tail = m + n - 1
        while p1 >= 0 or p2 >= 0:
            if p1 == -1:
                nums1[tail] = nums2[p2]
                p2 -= 1
            elif p2 == -1:
                nums1[tail] = nums1[p1]
                p1 -= 1
            elif nums1[p1] > nums2[p2]:
                nums1[tail] = nums1[p1]
                p1 -= 1
            else:
                nums1[tail] = nums2[p2]
                p2 -= 1
            tail -= 1
```



#### 算法

方法二中，之所以要使用临时变量，是因为如果直接合并到数组 $\textit{nums}_1$中，$\textit{nums}_1$中的元素可能会在取出之前被覆盖。那么如何直接避免覆盖 $\textit{nums}_1$中的元素呢？观察可知，$\textit{nums}_1$的后半部分是空的，可以直接覆盖而不会影响结果。因此可以指针设置为从后向前遍历，每次取两者之中的较大者放进 $\textit{nums}_1$的最后面。

严格来说，在此遍历过程中的任意一个时刻，$\textit{nums}_1$数组中有 $m-p_1-1$个元素被放入 $\textit{nums}_1$的后半部，$\textit{nums}_2$数组中有 $n-p_2-1$个元素被放入 $\textit{nums}_1$的后半部，而在指针 $p_1$的后面，$\textit{nums}_1$数组有 $m+n-p_1-1$个位置。由于

$$
m+n-p_1-1\geq m-p_1-1+n-p_2-1
$$
等价于

$$
p_2\geq -1
$$
永远成立，因此 $p_1$后面的位置永远足够容纳被插入的元素，不会产生 $p_1$的元素被覆盖的情况。

#### 复杂度分析

- 时间复杂度：O(m+n)。
  指针移动单调递减，最多移动 m+n 次，因此时间复杂度为 O(m+n)。
- 空间复杂度：O(1)。
  直接对数组 $\textit{nums}_1$原地修改，不需要额外空间。

