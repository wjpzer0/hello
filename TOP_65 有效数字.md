# 题目

**有效数字**（按顺序）可以分成以下几个部分：

1. 一个 小数 或者 整数
2. （可选）一个 'e' 或 'E' ，后面跟着一个 整数

**小数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（'+' 或 '-'）
2. 下述格式之一：
   1. 至少一位数字，后面跟着一个点 '.'
   2. 至少一位数字，后面跟着一个点 '.' ，后面再跟着至少一位数字
   3. 一个点 '.' ，后面跟着至少一位数字

**整数**（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（'+' 或 '-'）
2. 至少一位数字

部分有效数字列举如下：

["2", "0089", "-0.1", "+3.14", "4.", "-.9", "2e10", "-90E3", "3e+7", "+6e-1", "53.5e93", "-123.456e789"]
部分无效数字列举如下：

["abc", "1a", "1e", "e3", "99e2.5", "--6", "-+3", "95a54e53"]
给你一个字符串 s ，如果 s 是一个 有效数字 ，请返回 true 。

示例 1：

```
输入：s = "0"
输出：true
```


示例 2：

```
输入：s = "e"
输出：false
```


示例 3：

```
输入：s = "."
输出：false
```


示例 4：

```
输入：s = ".1"
输出：true
```


提示：

1 <= s.length <= 20
s 仅含英文字母（大写和小写），数字（0-9），加号 '+' ，减号 '-' ，或者点 '.' 。

## 我都解法

无，直接暴毙，没有任何想法。

## 其他解法

### 表驱动法

```Java
class Solution {
    public int make(char c) {
        switch(c) {
            case ' ': return 0;
            case '+':
            case '-': return 1;
            case '.': return 3;
            case 'e': return 4;
            default:
                if(c >= 48 && c <= 57) return 2;
        }
        return -1;
    }
    
    public boolean isNumber(String s) {
        int state = 0;
        int finals = 0b101101000;
        int[][] transfer = new int[][]{{ 0, 1, 6, 2,-1},
                                       {-1,-1, 6, 2,-1},
                                       {-1,-1, 3,-1,-1},
                                       { 8,-1, 3,-1, 4},
                                       {-1, 7, 5,-1,-1},
                                       { 8,-1, 5,-1,-1},
                                       { 8,-1, 6, 3, 4},
                                       {-1,-1, 5,-1,-1},
                                       { 8,-1,-1,-1,-1}};
        char[] ss = s.toCharArray();
        for(int i=0; i < ss.length; ++i) {
            int id = make(ss[i]);
            if (id < 0) return false;
            state = transfer[state][id];
            if (state < 0) return false;
        }
        return (finals & (1 << state)) > 0;
    }
}
```

本题可以采用《编译原理》里面的确定的有限状态机（DFA）解决。构造一个DFA并实现，构造方法可以先写正则表达式，然后转为 DFA，也可以直接写，我就是直接写的，虽然大概率不会是最简结构（具体请参考《编译器原理》图灵出版社），不过不影响解题。DFA 作为确定的有限状态机，比 NFA 更加实用，因为对于每一个状态接收的下一个字符，DFA 能确定唯一一条转换路径，所以使用简单的表驱动的一些方法就可以实现，并且只需要读一遍输入流，比起 NFA 需要回读在速度上会有所提升。

构建出来的状态机如封面图片所示（红色为 终止状态，蓝色为 中间状态）。根据《编译原理》的解释，DFA 从状态 0 接受串 s 作为输入。当s耗尽的时候如果当前状态处于中间状态，则拒绝；如果到达终止状态，则接受。

然后，根据 DFA 列出如下的状态跳转表，之后我们就可以采用 表驱动法 进行编程实现了。需要注意的是，这里面多了一个状态 8，是用于处理串后面的若干个多余空格的。所以，所有的终止态都要跟上一个状态 8。其中，有一些状态标识为-1，是表示遇到了一些意外的字符，可以直接停止后续的计算。状态跳转表如下：

| state | blank | +/-  | 0-9  | .    | e    | other |
| ----- | ----- | ---- | ---- | ---- | ---- | ----- |
| 0     | 0     | 1    | 6    | 2    | -1   | -1    |
| 1     | -1    | -1   | 6    | 2    | -1   | -1    |
| 2     | -1    | -1   | 3    | -1   | -1   | -1    |
| 3     | 8     | -1   | 3    | -1   | 4    | -1    |
| 4     | -1    | 7    | 5    | -1   | -1   | -1    |
| 5     | 8     | -1   | 5    | -1   | -1   | -1    |
| 6     | 8     | -1   | 6    | 3    | 4    | -1    |
| 7     | -1    | -1   | 5    | -1   | -1   | -1    |
| 8     | 8     | -1   | -1   | -1   | -1   | -1    |

状态图：

![DFA.jpg](https://pic.leetcode-cn.com/0683d701f2948a2bd8c235867c21a3aed5977691f129ecf34d681d43d57e339c-DFA.jpg)

### 一步一步来

```c
bool isNumber(char * s){
    bool bIsNum = false;

    while (' ' == *s) // 去除空格
    {
        s++;
    }

    if ('-' == *s || '+' == *s) // 允许出现一个符号
    {
        s++;
    }

    while (isdigit(*s)) // 整数部分
    {
        bIsNum = true;
        s++;
    }

    if ('.' == *s) // 允许出现一个小数点
    {
        s++;
    }

    while (isdigit(*s)) // 小数部分
    {
        bIsNum = true;
        s++;
    }

    if ((true == bIsNum && 'e' == *s) || (true == bIsNum && 'E' == *s)) // 允许出现科学计数法，其前必须为数字
    {
        s++;
        bIsNum = false;

        if ('+' == *s || '-' == *s) // 幂次方允许出现一个符号
        {
            s++;
        }

        while (isdigit(*s)) // 幂次方部分
        {
            s++;
            bIsNum = true;
        }
    }

    while (' ' == *s)
    {
        s++;
    }

    return '\0' == *s && bIsNum;
}
```

### 遍历目标字符串

```java
class Solution {
    public boolean isNumber(String s) {
        if(s==null||s.length()==0) return false;
        boolean numSeen=false;
        boolean dotSeen=false;
        boolean eSeen=false;
        char arr[]=s.trim().toCharArray();
        for(int i=0; i<arr.length; i++){
            if(arr[i]>='0'&&arr[i]<='9'){
                numSeen=true;
            }else if(arr[i]=='.'){
                if(dotSeen||eSeen){
                    return false;
                }
                dotSeen=true;
            }else if(arr[i]=='E'||arr[i]=='e'){
                if(eSeen||!numSeen){
                    return false;
                }
                eSeen=true;
                numSeen=false;
            }else if(arr[i]=='+'||arr[i]=='-'){
                if(i!=0&&arr[i-1]!='e'&&arr[i-1]!='E'){
                    return false;
                }
            }else{
                return false;
            }
        }
        return numSeen;
    }
}
```

先设定numSeen，dotSeen和eSeen三种boolean变量，分别代表是否出现数字、点和E
然后遍历目标字符串
1.判断是否属于数字的0~9区间
2.遇到点的时候，判断前面是否有点或者E，都需要return false
3.遇到E的时候，判断前面数字是否合理，是否有E，并把numSeen置为false，防止E后无数字
4.遇到-+的时候，判断是否是第一个，如果不是第一个判断是否在E后面，都不满足则return false
5.其他情况都为false
最后返回numSeen的结果即可

### 正则表达式

```python
import re
class Solution:
    def isNumber(self, s: str) -> bool:
        pat = re.compile(r'^[\+\-]?(\d+\.\d+|\.\d+|\d+\.|\d+)(e[\+\-]?\d+)?$')
        return True if len(re.findall(pat, s.strip())) else False
```

利用正则表达式匹配，整个数可分为三部分匹配，拿-13.14e-520举例：

- 符号（-），正则：[\+\-]?。
- e前面的数字（13.14），正则：(\d+\.\d+|\.\d+|\d+\.|\d+)。这里分了4种情况考虑，且匹配有先后顺序（经调试，0.0，.0，0.，0都是有效的）：
  - 有小数点且小数点前后都有数字；
  - 有小数点且只有小数点前面有数字；
  - 有小数点且只有小数点后面有数字；
  - 没有小数点（整数）。
- e及其指数（e-520），正则：(e[\+\-]?\d+)?。e0也有效。

