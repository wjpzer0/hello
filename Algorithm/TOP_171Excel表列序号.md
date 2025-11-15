#### 题目

给定一个Excel表格中的列名称，返回其相应的列序号。

例如，

    A -> 1
    B -> 2
    C -> 3
    ...
    Z -> 26
    AA -> 27
    AB -> 28 
    ...
示例 1:

```
输入: "A"
输出: 1
```


示例 2:

```
输入: "AB"
输出: 28
```


示例 3:

```
输入: "ZY"
输出: 701
```

#### 我的解法

```python
class Solution:
    def titleToNumber(self, s: str) -> int:
        num = 0
        for i in s:
            num = num + ord(i) - ord("A") + 1
            num = num * 26
        num = num//26
        return num
```

简化

```python
class Solution(object):
    def titleToNumber(self, s):
        #26进制转10进制
        ans = 0
        for x in s:
            ans *= 26
            ans += ord(x)-ord('A')+1
        return ans
```

#### 其他解法

###### 进制转换

```java
class Solution {
    public int titleToNumber(String s) {
        int ans = 0;
        for(int i=0;i<s.length();i++) {
            int num = s.charAt(i) - 'A' + 1;
            ans = ans * 26 + num;
        }
        return ans;
    }
}
```

初始化结果 ans = 0，遍历时将每个字母与 A 做减法，因为 A 表示 1，所以减法后需要每个数加 1，计算其代表的数值 num = 字母 - ‘A’ + 1
因为有 26 个字母，所以相当于 26 进制，每 26 个数则向前进一位
所以每遍历一位则ans = ans * 26 + num
以 ZY 为例，Z 的值为 26，Y 的值为 25，则结果为 26 * 26 + 25=701
时间复杂度：O(n)

![img](https://pic.leetcode-cn.com/a5e8e39fa19491e3e1d82c6aba3dec24e080c368d0400bf57012548b0fdb2af4-frame_00002.png)

![img](https://pic.leetcode-cn.com/da62003ebc140532fe1e42ff2c46d5c920101d6de50fd3c6910eee1e9d9c7df5-frame_00003.png)