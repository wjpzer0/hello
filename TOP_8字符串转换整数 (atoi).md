请你来实现一个 atoi 函数，使其能将字符串转换成整数。
首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：
如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。
注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。
在任何情况下，若函数不能进行有效的转换时，请返回 0 。

提示：
本题中的空白字符只包括空格字符 ' ' 。
假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (2^31 − 1) 或 INT_MIN (−2^31) 

示例 1:
输入: "42"
输出: 42

示例 2:
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。

示例 3:
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。

示例 4:
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。

示例 5:
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−2^31) 。

#### 我的解法：

未完成，没有分析步骤，程序混乱，多次尝试

#### 更优的解法:

###### 正则表达式

```python
import re
class Solution:
    def myAtoi(self, str: str) -> int:
        INT_MAX = 2147483647    
        INT_MIN = -2147483648
        str = str.lstrip()      #清除左边多余的空格
        num_re = re.compile(r'^[\+\-]?\d+')   #设置正则规则
        num = num_re.findall(str)   #查找匹配的内容
        num = int(*num) #由于返回的是个列表，解包并且转换成整数
        return max(min(num,INT_MAX),INT_MIN)    #返回值
```

^：匹配字符串开头
[\+\-]：代表一个+字符或-字符
?：前面一个字符可有可无
\d：一个数字
+：前面一个字符的一个或多个
\D：一个非数字字符
*：前面一个字符的0个或多个
max(min(数字, 2**31 - 1), -2**31) 用来防止结果越界

###### 正常遍历

```python
class Solution:
    def myAtoi(self, str: str) -> int:
        i=0
        n=len(str)
        while i<n and str[i]==' ':
            i=i+1
        if n==0 or i==n:
            return 0
        flag=1
        if str[i]=='-':
            flag=-1
        if str[i]=='+' or str[i]=='-':
            i=i+1
        INT_MAX=2**31-1
        INT_MIN=-2**31
        ans=0
        while i<n and '0'<=str[i]<='9':
            ans=ans*10+int(str[i])-int('0')
            i+=1
            if(ans-1>INT_MAX):
                break
        ans=ans*flag
        if ans>INT_MAX:
            return INT_MAX
        return INT_MIN if ans<INT_MIN else ans
```

1、去掉前导空格
2、再是处理正负号
3、识别数字，注意越界情况
根据题目思路，我们先去掉空格，然后判断是否有正负号，利用flag标记，最后开始匹配数字并将数字记录在ans里面，并结合flag返回值；时间复杂度O（n），空间复杂度O（1）
这里关于ans衍生出一个问题，如何处理越界问题：
1.如果ans可以是long型，比较temp-1>INT_MAX即可，如同我在C++和python语言所体现的
2.如果ans只能是int型，以结果为整数为列：如果有ans=ans10+pop，则比较ans10+pop>INT_MAX这里两边都不能溢出，乘10累计都可能溢出，我们需要反向思考变除变减比较，移项有
ans>(INT_MAX-pop)/10，但这个式子应该只在python中适用，因为他的除法是正常的，其他会向下取整，所以对于其他语言，可以对INT_MAX/10,再把pop单独拿出来讨论即可，如同我在java中体现的

###### 有限状态机

INT_MAX=2**31-1
INT_MIN=-2**31

```python
class Automata:
    def _init_(self):
        self.state="start"
        self.ans=0
        self.sign=1
        self.table={
            "start":["start","sign","innum","end"],
            "sign":["end","end","innum","end"],
            "innum":["end","end","innum","end"],
            "end":["end","end","end","end"],
        }
    def get_s(self,c):
        if c.isspace():
            return 0
        if c=='+' or c=='-':
            return 1
        if c.isdigit():
            return 2
        return 3
    def get(self,c):
        self.state=self.table[self.state][self.get_s(c)]
        if self.state=="innum":
            self.ans=self.ans*10+int(c)
            self.ans= min(self.ans,INT_MAX) if self.sign==1 else min(self.ans,-INT_MIN)
        if self.state=="sign":
            if c=='-':
                sign=-1
class Solution:
    def myAtoi(self, str: str) -> int:
        Auto=Automata()
        for c in str:
            Auto.get(c)
        return Auto.ans*Auto.sign
```

```python
        ' '         +/-         number         other
start	    start	    signed	    in_number	    end
signed	    end	        end	        in_number	    end
in_number	end	        end	        in_number	    end
end	        end	        end	        end	            end
```




描述
lstrip() 方法用于截掉字符串左边的空格或指定字符。

语法
lstrip()方法语法：
str.lstrip([chars])

参数
chars --指定截取的字符。

返回值
返回截掉字符串左边的空格或指定字符后生成的新字符串。

实例
以下实例展示了lstrip()的使用方法：
#!/usr/bin/python
str = "     this is string example....wow!!!     ";
print str.lstrip();
str = "88888888this is string example....wow!!!8888888";
print str.lstrip('8');

以上实例输出结果如下：
this is string example....wow!!!
this is string example....wow!!!8888888



描述
Python isspace() 方法检测字符串是否只由空格组成。

语法
isspace()方法语法：
str.isspace()

参数
无。

返回值
如果字符串中只包含空格，则返回 True，否则返回 False.

实例
以下实例展示了isspace()方法的实例：
#!/usr/bin/python
str = "       "; 
print str.isspace();
str = "This is string example....wow!!!";
print str.isspace();

以上实例输出结果如下：
True
False

描述
isdigit() 方法检测字符串是否只由数字组成。

语法
isdigit()方法语法：
str.isdigit()

参数
无。

返回值
如果字符串只包含数字则返回 True 否则返回 False。

实例
以下实例展示了isdigit()方法的实例：
#!/usr/bin/python
str = "123456";  # Only digit in this string
print str.isdigit();
str = "this is string example....wow!!!";
print str.isdigit();

以上实例输出结果如下：
True
False