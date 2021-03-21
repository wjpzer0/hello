判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

示例 1:
输入: 121
输出: true

示例 2:
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。

示例 3:
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。

进阶:
你能不将整数转为字符串来解决这个问题吗？

#### 我的解法

```python
字符串
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if str(x) == str(x)[::-1]:
            print(bool(1)) 
        else:
            print(bool())

数字
class Solution:
    def isPalindrome(self, x: int) -> bool:
        t = x
        n = 0
        m = 0
        while x > 0:
            m = x % 10
            n = n * 10 + m
            x = x //10
        return n == t 
```



#### 更优的解法

```python
字符串
class Solution:
    def isPalindrome(self, x: int) -> bool:
        s=str(x)
        return s[::-1]==s

数字
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0 or (x % 10 == 0 and x != 0):
            return False
        revertedNumber = 0
        while x > revertedNumber:
            revertedNumber = revertedNumber * 10 + x % 10
            x //= 10
        return x == revertedNumber or x == revertedNumber // 10
```



首先，我们应该处理一些临界情况。所有负数都不可能是回文，例如：-123 不是回文，因为 - 不等于 3。
所以我们可以对所有负数返回 false。除了 0 以外，所有个位是 0 的数字不可能是回文，因为最高位不等于 0。
所以我们可以对所有大于 0 且个位是 0 的数字返回 false。
现在，让我们来考虑如何反转后半部分的数字。
对于数字 1221，如果执行 1221 % 10，我们将得到最后一位数字 1，要得到倒数第二位数字，我们可以先通过除以 10 把最后一位数字从 1221 中移除，
1221 / 10 = 122，再求出上一步结果除以 10 的余数，122 % 10 = 2，就可以得到倒数第二位数字。
如果我们把最后一位数字乘以 10，再加上倒数第二位数字，1 * 10 + 2 = 12，就得到了我们想要的反转后的数字。
如果继续这个过程，我们将得到更多位数的反转数字。
现在的问题是，我们如何知道反转数字的位数已经达到原始数字位数的一半？
由于整个过程我们不断将原始数字除以 10，然后给反转后的数字乘上 10，所以，当原始数字小于或等于反转后的数字时，就意味着我们已经处理了一半位数的数字了

复杂度分析
时间复杂度：O(logn)，对于每次迭代，我们会将输入除以 1010，因此时间复杂度为 O(logn)。
空间复杂度：O(1)。我们只需要常数空间存放若干变量。

