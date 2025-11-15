给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。
请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。
你可以假设 nums1 和 nums2 不会同时为空。

示例 1:
nums1 = [1, 3]
nums2 = [2]
则中位数是 2.0

示例 2:
nums1 = [1, 2]
nums2 = [3, 4]
则中位数是 (2 + 3)/2 = 2.5

#### 我的解法

```python
class Solution(object):
    def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        s_nums = nums1 + nums2
        s_nums.sort()
        if len(s_nums) % 2 == 0:
            i = s_nums[len(s_nums)//2-1 ] + s_nums[len(s_nums)//2 ]
            i=float(i)/2
        else:
            i = s_nums[len(s_nums)//2]
        return i注：未做到算法的时间复杂度为 O(log(m + n))
```

#### 更优的解法

class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        def getKthElement(k):
            """
            - 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
            - 这里的 "/" 表示整除
            - nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
            - nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
            - 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
            - 这样 pivot 本身最大也只能是第 k-1 小的元素
            - 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
            - 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
            - 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
            """
            

```python
index1, index2 = 0, 0
while True:
    # 特殊情况
    if index1 == m:
        return nums2[index2 + k - 1]
    if index2 == n:
        return nums1[index1 + k - 1]
    if k == 1:
        return min(nums1[index1], nums2[index2])

    # 正常情况
    newIndex1 = min(index1 + k // 2 - 1, m - 1)
    newIndex2 = min(index2 + k // 2 - 1, n - 1)
    pivot1, pivot2 = nums1[newIndex1], nums2[newIndex2]
    if pivot1 <= pivot2:
        k -= newIndex1 - index1 + 1
        index1 = newIndex1 + 1
    else:
        k -= newIndex2 - index2 + 1
        index2 = newIndex2 + 1

m, n = len(nums1), len(nums2)
totalLength = m + n
if totalLength % 2 == 1:
return getKthElement((totalLength + 1) // 2)
else:
return (getKthElement(totalLength // 2) + getKthElement(totalLength // 2 + 1)) / 2
```

复杂度分析

时间复杂度：O(log(m+n))，其中 m和 n分别是数组 nums1 和 nums2 的长度。
初始时有 k=(m+n)/2 或 k=(m+n)/2+1，每一轮循环可以将查找范围减少一半，因此时间复杂度是 O(log(m+n))。
空间复杂度：O(1)。

```python
lass Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        if len(nums1) > len(nums2):
            return self.findMedianSortedArrays(nums2, nums1)
        infinty = 2**40
        m, n = len(nums1), len(nums2)
        left, right, ansi = 0, m, -1
        # median1：前一部分的最大值
        # median2：后一部分的最小值
        median1, median2 = 0, 0
        while left <= right:
            # 前一部分包含 nums1[0 .. i-1] 和 nums2[0 .. j-1]
            # // 后一部分包含 nums1[i .. m-1] 和 nums2[j .. n-1]
            i = (left + right) // 2
            j = (m + n + 1) // 2 - i
            # nums_im1, nums_i, nums_jm1, nums_j 分别表示 nums1[i-1], nums1[i], nums2[j-1], nums2[j]
            nums_im1 = (-infinty if i == 0 else nums1[i - 1])
            nums_i = (infinty if i == m else nums1[i])
            nums_jm1 = (-infinty if j == 0 else nums2[j - 1])
            nums_j = (infinty if j == n else nums2[j])
            if nums_im1 <= nums_j:
                ansi = i
                median1, median2 = max(nums_im1, nums_jm1), min(nums_i, nums_j)
                left = i + 1
                # 下一轮搜索的区间 [i + 1, right]
            else:
                right = i - 1
                # 下一轮搜索的区间 [left, i - 1]
        return float(median1 + median2) / 2 if (m + n) % 2 == 0 else median1
```

复杂度分析

时间复杂度：O(logmin(m,n)))，其中 m 和 n 分别是数组 nums1 和 nums2 的长度。
查找的区间是 [0, m]，而该区间的长度在每次循环之后都会减少为原来的一半。
所以，只需要执行logm 次循环。
由于每次循环中的操作次数是常数，所以时间复杂度为 O(logm)。
由于我们可能需要交换nums1和nums2 使得nm≤n，因此时间复杂度是 O(logmin(m,n)))。
空间复杂度：O(1)
