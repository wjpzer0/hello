给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

示例 1:
输入: 123
输出: 321

 示例 2:
输入: -123
输出: -321

示例 3:
输入: 120
输出: 21
注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

#### 我的解法

```python
class Solution:
    def reverse(self, x: int) -> int:
        s = str(x)
        if s[0] =="-":
            new = "-" + s[1:][::-1]
            new_x = int(new)
        else:
            new = s[::-1]
            new_x = int(new)
        if new_x < -2**31 or new_x > 2**31 -1:
            return 0
        else:
            return new_x
```

#### 更优解法

解题思路：
给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。这题难度不大很很适合新手练手。

###### 暴力法：

好像这题也不存在什么暴力法的，只是用简单的思路来处理一下：
直接来：

```python
def reverse_force(self, x: int) -> int:
        if -10 < x < 10:
            return x
        str_x = str(x)
        if str_x[0] != "-":
            str_x = str_x[::-1]
            x = int(str_x)
        else:
            str_x = str_x[:0:-1]
            x = int(str_x)
            x = -x
        return x if -2147483648 < x < 2147483647 else 0
```

优化解
我们可以一次构建反转整数的一位数字。在这样做的时候，我们可以预先检查向原整数附加另一位数字是否会导致溢出。
反转整数的方法可以与反转字符串进行类比。
我们想重复 “弹出” x 的最后一位数字，并将它 “推入” 到 res 的后面。最后，res 将与 x 相反。

###### 优化解：

时间复杂度：O(log(x))，x中大约有 log10(x) 位数字。
空间复杂度：O(1)

```python
def reverse_better(
        self, 
        x: int) -> int:
        y, res = abs(x), 0
        # 则其数值范围为 [−2^31,  2^31 − 1]
        boundry = (1<<31) -1 if x>0 else 1<<31
        while y != 0:
            res = res*10 +y%10
            if res > boundry :
                return 0
            y //=10
        return res if x >0 else -res
```

