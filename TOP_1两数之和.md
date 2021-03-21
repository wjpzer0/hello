给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

#### 我的解法

```python
nums = [3,3]
target = 6
for i in nums:
    print(nums.index(i)+1)
    for j in nums[(nums.index(i))+1:]:
        print(nums.index(j))
        if i + j == target:
            print([i,j])
```

#### 更优的解法

解题关键主要是想找到 num2 = target - num1，是否也在 list 中，那么就需要运用以下两个方法：
num2 in nums，返回 True 说明有戏
nums.index(num2)，查找 num2 的索引



```python
nums = [3,3]
target = 6
lens = len(nums)
j=-1
for i in range(lens):
    if (target - nums[i]) in nums:
        if (nums.count(target - nums[i]) == 1)&(target - nums[i] == nums[i]):#如果num2=num1,且nums中只出现了一次，说明找到是num1本身。
            continue
        else:
            j = nums.index(target - nums[i],i+1) #index(x,i+1)是从num1后的序列后找num2                
            break
if j>0:
    print([i,j])
else:
    print([])
```


解题思路是在方法一的基础上，优化解法。
想着，num2 的查找并不需要每次从 nums 查找一遍，只需要从 num1 位置之前或之后查找即可。
但为了方便 index 这里选择从 num1 位置之前查找：

```python
def twoSum(nums, target):
    lens = len(nums)
    j=-1
    for i in range(1,lens):
        temp = nums[:i]
        if (target - nums[i]) in temp:
            j = temp.index(target - nums[i])
            break
    if j>=0:
        return [j,i]
```

参考了大神们的解法，通过哈希来求解，这里通过字典来模拟哈希查询的过程。
个人理解这种办法相较于方法一其实就是字典记录了 num1 和 num2 的值和位置，而省了再查找 num2 索引的步骤。

```python
def twoSum(nums, target):
    hashmap={}
    for ind,num in enumerate(nums):
        hashmap[num] = ind
    for i,num in enumerate(nums):
        j = hashmap.get(target - num)
        if j is not None and i!=j:
            return [i,j]
```

类似方法二，不需要 mun2 不需要在整个 dict 中去查找。
可以在 num1 之前的 dict 中查找，因此就只需要一次循环可解决。

```python
def twoSum(nums, target):
    hashmap={}
    for i,num in enumerate(nums):
        if hashmap.get(target - num) is not None:
            return [i,hashmap.get(target - num)]
        hashmap[num] = i #这句不能放在if语句之前，解决list中有重复值或target-num=num的情况
```

排序＋双指针

class Solution:

```python
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        temp=nums.copy()
        temp.sort()
        i=0
        j=len(nums)-1
        while i<j:
            if (temp[i]+temp[j])>target:
                j=j-1
            elif (temp[i]+temp[j])<target:
                i=i+1
            else:
                break
        p=nums.index(temp[i])
        nums.pop(p)
        k=nums.index(temp[j])
        if k>=p:
            k=k+1
        return [p,k]
```

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。
语法
以下是 enumerate() 方法的语法:
enumerate(sequence, [start=0])
参数
sequence -- 一个序列、迭代器或其他支持迭代对象。
start -- 下标起始位置。
返回值

```
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
>>> [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))       # 小标从 1 开始
>>> [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]

seq = ['one', 'two', 'three']
for i, element in enumerate(seq):
    print(i, element)
0 one
1 two
2 three
```

