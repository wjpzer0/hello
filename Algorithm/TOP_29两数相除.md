#### 题目

给定两个整数，被除数 dividend 和除数 divisor。将两数相除，要求不使用乘法、除法和 mod 运算符。

返回被除数 dividend 除以除数 divisor 得到的商。

整数除法的结果应当截去（truncate）其小数部分，例如：truncate(8.345) = 8 以及 truncate(-2.7335) = -2

示例 1:

```
输入: dividend = 10, divisor = 3
输出: 3
解释: 10/3 = truncate(3.33333..) = truncate(3) = 3
```


示例 2:

```
输入: dividend = 7, divisor = -3
输出: -2
解释: 7/-3 = truncate(-2.33333..) = -2
```


提示：

- 被除数和除数均为 32 位有符号整数。
- 除数不为 0。
- 假设我们的环境只能存储 32 位有符号整数，其数值范围是 [−2^31,  2^31 − 1]。本题中，如果除法结果溢出，则返回 2^31 − 1。

#### 我的解法

```python
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        a = abs(dividend)
        b = abs(divisor)
        quotient = 0
        while a >= b:
            a = a - b
            quotient += 1
        if (dividend ^ divisor) < 0:
            quotient = -quotient
        return quotient
```

超时

#### 其他解法

###### 位运算

```java
public int divide(int dividend, int divisor) {
        if (dividend == 0) {
            return 0;
        }
        if (dividend == Integer.MIN_VALUE && divisor == -1) {
            return Integer.MAX_VALUE;
        }
        boolean negative;
        negative = (dividend ^ divisor) <0;//用异或来计算是否符号相异
        long t = Math.abs((long) dividend);
        long d= Math.abs((long) divisor);
        int result = 0;
        for (int i=31; i>=0;i--) {
            if ((t>>i)>=d) {//找出足够大的数2^n*divisor
                result+=1<<i;//将结果加上2^n
                t-=d<<i;//将被除数减去2^n*divisor
            }
        }
        return negative ? -result : result;//符号相异取反
    }
```

```python
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        INT_MAX = 2 ** 31 - 1
        INT_MIN = -2 ** 31
        if dividend == 0: return 0
        flag = (divisor ^ dividend) < 0  # True 表示结果为负

        if dividend == INT_MIN and divisor == -1: return INT_MAX
        t = abs(dividend)
        d = abs(divisor)

        res = 0
        for i in range(31, -1, -1):
            if (t >> i) >= d:
                res += 1 << i
                t = t - (d << i)
        if flag:
            return -res
        else:
            return res
```

**解题思路：**

这题是除法，所以先普及下除法术语
商，公式是：(被除数-余数)÷除数=商，记作：被除数÷除数=商...余数，是一种数学术语。
在一个除法算式里，被除数、余数、除数和商的关系为：(被除数-余数)÷除数=商，记作：被除数÷除数=商...余数，
进而推导得出：商×除数+余数=被除数。
要求商，我们首先想到的是减法，能被减多少次，那么商就为多少，但是明显减法的效率太低
那么我们可以用位移法，因为计算机在做位移时效率特别高，向左移1相当于乘以2，向右位移1相当于除以2
我们可以把一个dividend（被除数）先除以2^n ，n最初为31，不断减小n去试探,当某个n满足dividend/2^n>=divisor时，
表示我们找到了一个足够大的数，这个数\*divisor是不大于dividend的，所以我们就可以减去2^n个divisor，以此类推
我们可以以100/3为例
2^n  是1，2，4，8...2^31 这种数，当n为31时，这个数特别大，100/2^n是一个很小的数，肯定是小于3的，所以循环下来，
 当n=5时，100/32=3, 刚好是大于等于3的，这时我们将100-32*3=4，也就是减去了32个3，接下来我们再处理4，同样手法可以再减去一个3
所以一共是减去了33个3，所以商就是33
这其中得处理一些特殊的数，比如divisor是不能为0的，Integer.MIN_VALUE和Integer.MAX_VALUE

###### 列竖式算除法

有问题（要求写明白了32位有符号整数）会导致溢出

```python
class Solution:
    def divide(self, dividend: int, divisor: int) -> int:
        sign = (dividend > 0) ^ (divisor > 0)
        dividend = abs(dividend)
        divisor = abs(divisor)
        count = 0
        #把除数不断左移，直到它大于被除数
        while dividend >= divisor:
            count += 1
            divisor <<= 1
        result = 0
        while count > 0:
            count -= 1
            divisor >>= 1
            if divisor <= dividend:
                result += 1 << count #这里的移位运算是把二进制（第count+1位上的1）转换为十进制
                dividend -= divisor
        if sign: result = -result
        return result if -(1<<31) <= result <= (1<<31)-1 else (1<<31)-1 
```

**思路：**
让我们先回顾一下小学时，怎么通过列竖式的方法计算两个整数的除法，以 45/2 为例：

![2019-07-01 19-26-57屏幕截图.png](https://pic.leetcode-cn.com/06d1587045a44cafe8be84000aeced19cfc27ea09aabd8df3a89de6c5d2d8d7d-2019-07-01%2019-26-57%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

仔细观察不难发现，这种算法是把除法化归成移位和减法两种运算方法。对于 10 进制数，移位运算就是乘（左移）除（右移）10，而我们都知道计算机中的移位运算是乘（左移）除（右移）2，因为计算机是通过二进制的方法存储数的。这样，类比十进制，二进制的除法（仍以 45/2 为例）可以写作（注意，这里我们并没有用到乘除法）

![2019-07-01 19-26-02屏幕截图.png](https://pic.leetcode-cn.com/b0b567805574f94e215153555d7bed5c3de04f3555bbae0e9c767afaf421d13d-2019-07-01%2019-26-02%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

