#### 题目

给定一个正整数 n ，输出外观数列的第 n 项。
「外观数列」是一个整数序列，从数字 1 开始，序列中的每一项都是对前一项的描述。
你可以将其视作是由递归公式定义的数字字符串序列：
countAndSay(1) = "1"
countAndSay(n) 是对 countAndSay(n-1) 的描述，然后转换成另一个数字字符串。
前五项如下：
 ```
   1.     1
   2.     11
   3.     21
   4.     1211
   5.     111221
   第一项是数字 1 
   描述前一项，这个数是 1 即 “ 一 个 1 ”，记作 "11"
   描述前一项，这个数是 11 即 “ 二 个 1 ” ，记作 "21"
   描述前一项，这个数是 21 即 “ 一 个 2 + 一 个 1 ” ，记作 "1211"
   描述前一项，这个数是 1211 即 “ 一 个 1 + 一 个 2 + 二 个 1 ” ，记作 "111221"
 ```


   要 描述 一个数字字符串，首先要将字符串分割为 最小 数量的组，每个组都由连续的最多 相同字符 组成。然后对于每个组，先描述字符的数量，然后描述字符，形成一个描述组。要将描述转换为数字字符串，先将每组中的字符数量用数字替换，再将所有描述组连接起来。

![img](https://assets.leetcode.com/uploads/2020/10/23/countandsay.jpg)

示例 1：

```
输入：n = 1
输出："1"
解释：这是一个基本样例。
```

示例 2：

```
输入：n = 4
输出："1211"
解释：
countAndSay(1) = "1"
countAndSay(2) = 读 "1" = 一 个 1 = "11"
countAndSay(3) = 读 "11" = 二 个 1 = "21"
countAndSay(4) = 读 "21" = 一 个 2 + 一 个 1 = "12" + "11" = "1211"
```

提示：
1 <= n <= 30

#### 我的解法

无，思维混乱，没有清晰的逻辑思考

#### 其他解法

###### 不递归

```python
class Solution:
    def countAndSay(self, n: int) -> str:
        prev_person = '1'
        for i in range(1,n):
            next_person, num, count = '', prev_person[0], 1
            for j in range(1,len(prev_person)):
                if prev_person[j] == num:count += 1
                else:
                    next_person += str(count) + num
                    num = prev_person[j]
                    count = 1
            next_person += str(count) + num
            prev_person = next_person
        return prev_person
```

**循环含义**
每次外循环含义为给定上一个人报的数，求下一个人报的数
每次内循环为遍历上一个人报的数

**具体思路**
先设置上一人为'1'
开始外循环
每次外循环先置下一人为空字符串，置待处理的字符num为上一人的第一位，置记录出现的次数为1
开始内循环，遍历上一人的数，如果数是和num一致，则count增加。
若不一致，则将count和num一同添加到next_person报的数中，同时更新num和count
别忘了更新next_person的最后两个数为上一个人最后一个字符以及其出现次数！

###### 双指针 递归操作

```java
class Solution {
    public String countAndSay(int n) {
        // 递归终止条件
        if (n == 1) {
            return "1";
        }
        StringBuffer res = new StringBuffer();
        // 拿到上一层的字符串
        String str = countAndSay(n - 1);
        int length = str.length();
        // 开始指针为0
        int start = 0;
        // 注意这从起始条件要和下面长度统一
        for (int i = 1; i < length + 1; i++) {
            // 字符串最后一位直接拼接
            if (i == length) {
                res.append(i - start).append(str.charAt(start));
            // 直到start位的字符串和i位的字符串不同，拼接并更新start位
            } else if (str.charAt(i) != str.charAt(start) ) {
                res.append(i - start).append(str.charAt(start));
                start = i;
            }
        }
        return res.toString();
    }
}
```

利用递归和StringBuffer来解析，通过StringBuffer可以大幅度明显感知到其的效率，把数组长度加1来
比如 n=6时,那么用递归得到上一层的字符串str=“111221”
我们将start指向下标0,我们从下标1开始遍历,遍历到“2”下标3的时候,sb拼上(3-0)个1即sb.append(3).append(1),将start指针指向下标3,接着重复以上操作,直到到达str的最后一位,sb直接拼上即可。

