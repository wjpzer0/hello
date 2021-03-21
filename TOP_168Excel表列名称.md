#### 题目

给定一个正整数，返回它在 Excel 表中相对应的列名称。

例如，

    1 -> A
    2 -> B
    3 -> C
    ...
    26 -> Z
    27 -> AA
    28 -> AB 
    ...
示例 1:

```
输入: 1
输出: "A"
```


示例 2:

```
输入: 28
输出: "AB"
```


示例 3:

```
输入: 701
输出: "ZY"
```

#### 我的解法

```python
class Solution:
    def convertToTitle(self, n: int) -> str:
        Strings = ''
        while n :
            modN = n % 26
            if modN == 0:
                String = 'Z'
                Strings = String + Strings 
                n = n // 26 - 1
            else:    
                String = chr(ord('Z') - 26 + modN)
                Strings =  String + Strings 
                n = n // 26
        return Strings
```

把刚好满足26倍数的值特殊处理

#### 其他解法

```java
class Solution {
    public String convertToTitle(int n) {
        StringBuffer sb = new StringBuffer();

        // 输出对应数字的序列号
        while (n > 0){
            // 这里参考leetcode171 ，因为正着计算加一 所以这里反着需要减一
            n -= 1;
            // 获得当前数字对应的字符 并加入到 StringBuffer中
            sb.append((char)('A' + (n % 26)));
            // 向下退一位
            n /= 26;
        }

        // 倒序输出字符串
        return sb.reverse().toString();
    }
}
```

![图片.png](https://pic.leetcode-cn.com/72b2a96e1526021c316b8017bbf82a193d4fc5b51d829cd742863eec1964b0b1-%E5%9B%BE%E7%89%87.png)