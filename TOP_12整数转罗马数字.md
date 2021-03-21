#### 题目

罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。
通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：
I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。

示例 1:
输入: 3
输出: "III"

示例 2:
输入: 4
输出: "IV"

示例 3:
输入: 9
输出: "IX"

示例 4:
输入: 58
输出: "LVIII"
解释: L = 50, V = 5, III = 3.

示例 5:
输入: 1994
输出: "MCMXCIV"
解释: M = 1000, CM = 900, XC = 90, IV = 4.

#### 我的解法

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        string = ""
        if num // 1000 > 0:
            string = string + "M"* (num // 1000)
            num = num % 1000
        if num // 100 > 0:
            if num // 100 == 4:
                string = string + "CD"
            elif num // 100 == 9:
                string = string + "CM"
            elif num // 100 == 5:
                string = string + "D"
            if num // 100 > 5 and num // 100 < 9:
                string = string + "D" + "C" * (num // 100 - 5)
            elif num // 100 > 0 and num // 100 < 4:
                string = string + "C" * (num // 100)
            num = num % 100
        if num // 10 > 0:
            if num // 10 == 4:
                string = string + "XL"
            elif num // 10 == 9:
                string = string + "XC"
            elif num // 10 == 5:
                string = string + "L"
            if num // 10 > 5 and num // 10 < 9:
                string = string + "L" + "X" * (num // 10 - 5)
            elif num // 10 > 0 and num // 10 < 4:
                string = string + "X" * (num // 10)
            num = num % 10
        if num > 0:
            if num == 4:
                string = string + "IV"
            elif num == 9:
                string = string + "IX"
            elif num == 5:
                string = string + "V"
            if num > 5 and num < 9:
                string = string + "V" + "I" * (num - 5)
            elif num > 0 and num < 4:
                string = string + "I" * (num)
        return string
```

繁琐，效率较高

#### 其他解法

###### 贪心

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        # 把阿拉伯数字与罗马数字可能出现的所有情况和对应关系，放在两个数组中
        # 并且按照阿拉伯数字的大小降序排列，这是贪心选择思想
        nums = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
        romans = ["M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"]

        index = 0
        res = ''
        while index < 13:
            # 注意：这里是等于号，表示尽量使用大的"面值"
            while num >= nums[index]:
                res += romans[index]
                num -= nums[index]
            index += 1
        return res
```

```java
public class Solution {

    public String intToRoman(int num) {
        // 把阿拉伯数字与罗马数字可能出现的所有情况和对应关系，放在两个数组中
        // 并且按照阿拉伯数字的大小降序排列，这是贪心选择思想
        int[] nums = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] romans = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        StringBuilder stringBuilder = new StringBuilder();
        int index = 0;
        while (index < 13) {
            // 特别注意：这里是等号
            while (num >= nums[index]) {
                // 注意：这里是等于号，表示尽量使用大的"面值"
                stringBuilder.append(romans[index]);
                num -= nums[index];
            }
            index++;
        }
        return stringBuilder.toString();
    }
}
```

**复杂度分析**
时间复杂度：O(1)O(1)。由于有一组有限的罗马数字，循环可以迭代多少次有一个硬上限。因此，我们说时间复杂度是常数的，即 O(1)O(1)。
空间复杂度：O(1)O(1)，使用的内存量不会随输入整数的大小而改变，因此是常数的。

###### 硬编码数字

```java
public String intToRoman(int num) {
    
    String[] thousands = {"", "M", "MM", "MMM"};
    String[] hundreds = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"}; 
    String[] tens = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
    String[] ones = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};
    
    return thousands[num / 1000] + hundreds[num % 1000 / 100] + tens[num % 100 / 10] + ones[num % 10];
}
```

复杂度分析
时间复杂度：O(1)O(1)。无论输入的大小，都会执行相同数量的操作。因此，时间复杂度是常数的。
空间复杂度：O(1)O(1)，虽然我们使用数组，但不管输入的大小，它们都是相同的大小。因此，它们是常数级空间。
