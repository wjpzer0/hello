#### 题目

编写一个函数来查找字符串数组中的最长公共前缀。
如果不存在公共前缀，返回空字符串 ""。

示例 1:
输入: ["flower","flow","flight"]
输出: "fl"

示例 2:
输入: ["dog","racecar","car"]
输出: ""

解释: 输入不存在公共前缀。
说明:

所有输入只包含小写字母 a-z 。

#### 我的解法

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        st = ""
        flag = True
        if len(strs) == 0:
            return st
        First = strs[0]
        num = 0
        while flag:
            for i in strs:
                if len(i) <= num:
                    flag = False
                    break
                if i[num] == First[num] :
                    flag = True
                else:
                    flag = False
                    break
            if flag:
                st = st + First[num]
            num +=1
        return st
```

#### 其他解法

###### 链表

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs.length == 0) 
            return "";
        String ans = strs[0];
        for(int i =1;i<strs.length;i++) {
            int j=0;
            for(;j<ans.length() && j < strs[i].length();j++) {
                if(ans.charAt(j) != strs[i].charAt(j))
                    break;
            }
            ans = ans.substring(0, j);
            if(ans.equals(""))
                return ans;
        }
        return ans;
    }
}
```

当字符串数组长度为 0 时则公共前缀为空，直接返回
令最长公共前缀 ans 的值为第一个字符串，进行初始化
遍历后面的字符串，依次将其与 ans 进行比较，两两找出公共前缀，最终结果即为最长公共前缀
如果查找过程中出现了 ans 为空的情况，则公共前缀不存在直接返回
时间复杂度：O(s)O(s)，s 为所有字符串的长度之和

###### 横向扫描

用LCP(S1.... Sn)表示字符串S1 ....Sn的最长公共前缀。
可以得到以下结论：LCP(S1.... Sn) = LCP(LCP(LCP(S1,  S2), S3), ...Sn)
基于该结论，可以得到一种查找字符串数组中的最长公共前缀的简单方法。依次遍历字符串数组中的每个字符串，对于每个遍历到的字符串，更新最长公共前缀，当遍历完所有的字符串以后，即可得到字符串数组中的最长公共前缀。

![fig1](https://assets.leetcode-cn.com/solution-static/14/14_fig1.png)

如果在尚未遍历完所有的字符串时，最长公共前缀已经是空串，则最长公共前缀一定是空串，因此不需要继续遍历剩下的字符串，直接返回空串即可。

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ""
        
        prefix, count = strs[0], len(strs)
        for i in range(1, count):
            prefix = self.lcp(prefix, strs[i])
            if not prefix:
                break
        
        return prefix

    def lcp(self, str1, str2):
        length, index = min(len(str1), len(str2)), 0
        while index < length and str1[index] == str2[index]:
            index += 1
        return str1[:index]
```
复杂度分析
时间复杂度：O(mn)O(mn)，其中 mm 是字符串数组中的字符串的平均长度，nn 是字符串的数量。最坏情况下，字符串数组中的每个字符串的每个字符都会被比较一次。
空间复杂度：O(1)O(1)。使用的额外空间复杂度为常数。

###### 纵向扫描

方法一是横向扫描，依次遍历每个字符串，更新最长公共前缀。另一种方法是纵向扫描。纵向扫描时，从前往后遍历所有字符串的每一列，比较相同列上的字符是否相同，如果相同则继续对下一列进行比较，如果不相同则当前列不再属于公共前缀，当前列之前的部分为最长公共前缀。

![fig2](https://assets.leetcode-cn.com/solution-static/14/14_fig2.png)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        int length = strs[0].length();
        int count = strs.length;
        for (int i = 0; i < length; i++) {
            char c = strs[0].charAt(i);
            for (int j = 1; j < count; j++) {
                if (i == strs[j].length() || strs[j].charAt(i) != c) {
                    return strs[0].substring(0, i);
                }
            }
        }
        return strs[0];
    }
}

```

复杂度分析
时间复杂度：O(mn)，其中 mm 是字符串数组中的字符串的平均长度，nn 是字符串的数量。最坏情况下，字符串数组中的每个字符串的每个字符都会被比较一次。
空间复杂度：O(1)。使用的额外空间复杂度为常数。

###### 分治

注意到 LCP 的计算满足结合律，有以下结论：

LCP(S1....Sn) = LCP(LCP(S1....Sk), LCP (S(k+1)....S_n))
其中 LCP(S1…S n) 是字符串 S1…Sn的最长公共前缀，1 < k < n1<k<n。

基于上述结论，可以使用分治法得到字符串数组中的最长公共前缀。对于问题 LCP(Si⋯S j)，可以分解成两个子问题 LCP(S i…S mid ) 与 LCP(Smid+1…Sj)，其中 mid= (i+j)/2。对两个子问题分别求解，然后对两个子问题的解计算最长公共前缀，即为原问题的解。

![fig3](https://assets.leetcode-cn.com/solution-static/14/14_fig3.png)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        } else {
            return longestCommonPrefix(strs, 0, strs.length - 1);
        }
    }

    public String longestCommonPrefix(String[] strs, int start, int end) {
        if (start == end) {
            return strs[start];
        } else {
            int mid = (end - start) / 2 + start;
            String lcpLeft = longestCommonPrefix(strs, start, mid);
            String lcpRight = longestCommonPrefix(strs, mid + 1, end);
            return commonPrefix(lcpLeft, lcpRight);
        }
    }

    public String commonPrefix(String lcpLeft, String lcpRight) {
        int minLength = Math.min(lcpLeft.length(), lcpRight.length());       
        for (int i = 0; i < minLength; i++) {
            if (lcpLeft.charAt(i) != lcpRight.charAt(i)) {
                return lcpLeft.substring(0, i);
            }
        }
        return lcpLeft.substring(0, minLength);
    }
}
```

复杂度分析
时间复杂度：O(mn)O(mn)，其中 mm 是字符串数组中的字符串的平均长度，nn 是字符串的数量。时间复杂度的递推式是 =T(n)=2⋅T(n/2)+O(m)，通过计算可得 =T(n)=O(mn)。
空间复杂度：O(m \log n)O(mlogn)，其中 mm 是字符串数组中的字符串的平均长度，nn 是字符串的数量。空间复杂度主要取决于递归调用的层数，层数最大为 \log nlogn，每层需要 mm 的空间存储返回结果。

###### 二分查找

显然，最长公共前缀的长度不会超过字符串数组中的最短字符串的长度。用 minLength 表示字符串数组中的最短字符串的长度，则可以在 [0,minLength] 的范围内通过二分查找得到最长公共前缀的长度。每次取查找范围的中间值 mid，判断每个字符串的长度为 mid 的前缀是否相同，如果相同则最长公共前缀的长度一定大于或等于 mid，如果不相同则最长公共前缀的长度一定小于mid，通过上述方式将查找范围缩小一半，直到得到最长公共前缀的长度。

![fig4](https://assets.leetcode-cn.com/solution-static/14/14_fig4.png)

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        int minLength = Integer.MAX_VALUE;
        for (String str : strs) {
            minLength = Math.min(minLength, str.length());
        }
        int low = 0, high = minLength;
        while (low < high) {
            int mid = (high - low + 1) / 2 + low;
            if (isCommonPrefix(strs, mid)) {
                low = mid;
            } else {
                high = mid - 1;
            }
        }
        return strs[0].substring(0, low);
    }

    public boolean isCommonPrefix(String[] strs, int length) {
        String str0 = strs[0].substring(0, length);
        int count = strs.length;
        for (int i = 1; i < count; i++) {
            String str = strs[i];
            for (int j = 0; j < length; j++) {
                if (str0.charAt(j) != str.charAt(j)) {
                    return false;
                }
            }
        }
        return true;
    }
}
```
复杂度分析
时间复杂度：O(mn \log m)O(mnlogm)，其中 mm 是字符串数组中的字符串的最小长度，nn 是字符串的数量。二分查找的迭代执行次数是 O(\log m)O(logm)，每次迭代最多需要比较 mnmn 个字符，因此总时间复杂度是 O(mn \log m)O(mnlogm)。
空间复杂度：O(1)O(1)。使用的额外空间复杂度为常数。

#### 不清楚的函数

语法格式：

```python
any(iterable)
```

描述：
如果 iterable 的任一元素为真值则返回 True，否则返回 False。
如果可迭代对象为空，返回 False。 

参数说明：
iterable —— 可迭代对象。 

返回值：
布尔值。

示例：
```python
>>> any(['a', 'b', 'c', 'd'])   # 列表list，元素都不为空或0
True
>>> any(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
True
>>> any([0, '', False])        # 列表list,元素全为0,'',false
False
>>> any(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
True
>>> any(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
True
>>> any((0, '', False))        # 元组tuple，元素全为0,'',false
False
>>> any([]) # 空列表
False
>>> any(()) # 空元组
False
```

 语法格式：

```python
all(iterable)
```

### 描述：

- 如果 iterable 的所有元素都为真值（或可迭代对象为空）则返回 True，否则返回 False。

### 参数说明：

- iterable —— 可迭代对象。

### 返回值： 

- 布尔值。

### 示例：

```python
>>> all('')      # 可迭代对象为空，返回 True
True
>>> all([])
True
>>> all(())
True
>>> all({})
True
>>> all('0')      # 0 作为整数其逻辑值为 False；作为字符串的中的元素，该元素为 True
True
>>> all([0, '1', 2, '0'])          # 整数 0，作为列表的元素，该元素的逻辑值为 False
False
>>> all(['', 1, 2, 3])     # 空字符串作为可迭代对象的元素，该元素的逻辑值为 False
False
>>> all([[], 1, 2, 3])
False
>>> all([(), 1, 2, 3])
False
>>> all([{}, 1, 2, 3])
False
```

 

