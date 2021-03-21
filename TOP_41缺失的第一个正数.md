#### 题目

给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。

示例 1:

```
输入: [1,2,0]
输出: 3
```


示例 2:

```
输入: [3,4,-1,1]
输出: 2
```


示例 3:

```
输入: [7,8,9,11,12]
输出: 1
```


提示：

你的算法的时间复杂度应为O(n)，并且只能使用常数级别的额外空间。

#### 我的解法

```python
class Solution:
    def firstMissingPositive(self, nums: List[int]) -> int:
        n = 1
        while True:
            if n in nums:
                n += 1
            else:
                break
        return n
```

没有做到提示中的内容

#### 其他解法

###### 哈希表（空间复杂度不符合要求）

```Java
import java.util.HashSet;
import java.util.Set;

public class Solution {

    public int firstMissingPositive(int[] nums) {
        int len = nums.length;

        Set<Integer> hashSet = new HashSet<>();
        for (int num : nums) {
            hashSet.add(num);
        }

        for (int i = 1; i <= len ; i++) {
            if (!hashSet.contains(i)){
                return i;
            }
        }

        return len + 1;
    }
}
```

按照刚才我们读例子的思路，其实我们只需从最小的正整数 11 开始，依次判断 2、 3、4 直到数组的长度 N 是否在数组中；
如果当前考虑的数不在这个数组中，我们就找到了这个缺失的最小正整数；
由于我们需要依次判断某一个正整数是否在这个数组里，我们可以先把这个数组中所有的元素放进哈希表。接下来再遍历的时候，就可以以 O(1)的时间复杂度判断某个正整数是否在这个数组；
由于题目要求我们只能使用常数级别的空间，而哈希表的大小与数组的长度是线性相关的，因此空间复杂度不符合题目要求。

**复杂度分析：**

时间复杂度：O(N)，这里 N 表示数组的长度。第 1 次遍历了数组，第 2 次遍历了区间 [1, len] 里的元素。
空间复杂度：O(N)，把 N 个数存在哈希表里面，使用了 N 个空间。

###### 二分查找（时间复杂度不符合要求）



根据刚才的

```Java
import java.util.Arrays;

public class Solution {

    public int firstMissingPositive(int[] nums) {
        int len = nums.length;
        Arrays.sort(nums);

        for (int i = 1; i <= len; i++) {
            int res = binarySearch(nums, i);
            if (res == -1) {
                return i;
            }
        }
        return len + 1;
    }

    private int binarySearch(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;
        while (left <= right) {
            int mid = (left + right) >>> 1;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }
}
```

分析，这个问题其实就是要我们查找一个元素，而查找一个元素，如果是在有序数组中查找，会快一些；
因此我们可以将数组先排序，再使用二分查找法从最小的正整数 11 开始查找，找不到就返回这个正整数；
这个思路需要先对数组排序，而排序使用的时间复杂度是 O(NlogN)，是不符合这个问题的时间复杂度要求。

**复杂度分析：**

时间复杂度：O(NlogN)，这里 NN 表示数组的长度。时间复杂度主要消耗在排序上，排序使用 O(NlogN)。二分查找使用每一步使用的时间复杂度是 O(logN)，整体上仍然是 O(NlogN)；
空间复杂度：O(1)。

###### 将数组视为哈希表

**注意**：理解下面代码 `nums[nums[i] - 1] != nums[i]` 的作用。

```python
from typing import List
class Solution:
    # 3 应该放在索引为 2 的地方
    # 4 应该放在索引为 3 的地方
    def firstMissingPositive(self, nums: List[int]) -> int:
        size = len(nums)
        for i in range(size):
            # 先判断这个数字是不是索引，然后判断这个数字是不是放在了正确的地方
            while 1 <= nums[i] <= size and nums[i] != nums[nums[i] - 1]:
                self.__swap(nums, i, nums[i] - 1)
        for i in range(size):
            if i + 1 != nums[i]:
                return i + 1
        return size + 1

    def __swap(self, nums, index1, index2):
        nums[index1], nums[index2] = nums[index2], nums[index1]
```

说明：Python 里可以这样写 nums[nums[i] - 1], nums[i] = nums[i], nums[nums[i] - 1] ，但是这里赋值有先后顺序，写成 nums[i], nums[nums[i] - 1] = nums[nums[i] - 1], nums[i], 就会出错。建议封装成单独的函数，避免出错。

```Java
public class Solution {

    public int firstMissingPositive(int[] nums) {
        int len = nums.length;

        for (int i = 0; i < len; i++) {
            while (nums[i] > 0 && nums[i] <= len && nums[nums[i] - 1] != nums[i]) {
                // 满足在指定范围内、并且没有放在正确的位置上，才交换
                // 例如：数值 3 应该放在索引 2 的位置上
                swap(nums, nums[i] - 1, i);
            }
        }

        // [1, -1, 3, 4]
        for (int i = 0; i < len; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        // 都正确则返回数组长度 + 1
        return len + 1;
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```



- 由于题目要求我们「只能使用常数级别的空间」，而要找的数一定在 [1, N + 1] 左闭右闭（这里 N 是数组的长度）这个区间里。因此，我们可以就把原始的数组当做哈希表来使用。事实上，哈希表其实本身也是一个数组；
  我们要找的数就在 [1, N + 1] 里，最后 N + 1 这个元素我们不用找。因为在前面的 N 个元素都找不到的情况下，我们才返回 N + 1；
- 那么，我们可以采取这样的思路：就把 11 这个数放到下标为 00 的位置， 22 这个数放到下标为 11 的位置，按照这种思路整理一遍数组。然后我们再遍历一次数组，第 11 个遇到的它的值不等于下标的那个数，就是我们要找的缺失的第一个正数。
- 这个思想就相当于我们自己编写哈希函数，这个哈希函数的规则特别简单，那就是数值为 i 的数映射到下标为 i - 1 的位置。

![0041-14.png](https://pic.leetcode-cn.com/1e4f3f1c9a6fb37c2aa515069508f5f3ef9d72cc55b586790f9bec9705052d17-0041-14.png)

复杂度分析：

- 时间复杂度：O(N)，这里 N 是数组的长度。
  说明：while 循环不会每一次都把数组里面的所有元素都看一遍。如果有一些元素在这一次的循环中被交换到了它们应该在的位置，那么在后续的遍历中，由于它们已经在正确的位置上了，代码再执行到它们的时候，就会被跳过。

  最极端的一种情况是，在第 1 个位置经过这个 while 就把所有的元素都看了一遍，这个所有的元素都被放置在它们应该在的位置，那么 for 循环后面的部分的 while 的循环体都不会被执行。

  平均下来，每个数只需要看一次就可以了，while 循环体被执行很多次的情况不会每次都发生。这样的复杂度分析的方法叫做均摊复杂度分析。

  最后再遍历了一次数组，最坏情况下要把数组里的所有的数都看一遍，因此时间复杂度是 O(N)。

- 空间复杂度：O(1)。


