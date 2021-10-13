# 题目

给你一个整数 `n` ，找出从 `1` 到 `n` 各个整数的 Fizz Buzz 表示，并用字符串数组 `answer`（**下标从 1 开始**）返回结果，其中：

- `answer[i] == "FizzBuzz"` 如果 `i` 同时是 `3` 和 `5` 的倍数。
- `answer[i] == "Fizz"` 如果 `i` 是 `3` 的倍数。
- `answer[i] == "Buzz"` 如果 `i` 是 `5` 的倍数。
- `answer[i] == i` 如果上述条件全不满足。

**示例 1：**

```
输入：n = 3
输出：["1","2","Fizz"]
```

**示例 2：**

```
输入：n = 5
输出：["1","2","Fizz","4","Buzz"]
```

**示例 3：**

```
输入：n = 15
输出：["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz"]
```

**提示：**

- $1 <= n <= 10^4$

## 我的解法

```python
class Solution:
    def fizzBuzz(self, n: int) -> List[str]:
        ans = [0] * (n + 1)
        for i in range(1,n + 1):
            if i % 15 == 0:
                ans[i] = "FizzBuzz"
            elif i % 3 == 0:
                ans[i] = "Fizz"
            elif i % 5 == 0:
                ans[i] = "Buzz"
            else:
                ans[i] = str(i)
        return ans[1:]
```

解法思路清晰，代码简单！

## 其他解法

### 模拟 + 字符串拼接

```c++
class Solution {
public:
    vector<string> fizzBuzz(int n) {
        vector<string> answer;
        for (int i = 1; i <= n; i++) {
            string curr;
            if (i % 3 == 0) {
                curr　+= "Fizz";
            }
            if (i % 5 == 0) {
                curr += "Buzz";
            }
            if (curr.size() == 0) {
                curr += to_string(i);
            }            
            answer.emplace_back(curr);
        }
        return answer;
    }
};
```

```java
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> answer = new ArrayList<String>();
        for (int i = 1; i <= n; i++) {
            StringBuffer sb = new StringBuffer();
            if (i % 3 == 0) {
                sb.append("Fizz");
            }
            if (i % 5 == 0) {
                sb.append("Buzz");
            }
            if (sb.length() == 0) {
                sb.append(i);
            }
            answer.add(sb.toString());
        }
        return answer;
    }
}
```

```python
class Solution:
    def fizzBuzz(self, n: int) -> List[str]:
        ans = []
        for i in range(1, n + 1):
            s = ""
            if i % 3 == 0:
                s += "Fizz"
            if i % 5 == 0:
                s += "Buzz"
            if s == "":
                s = str(i)
            ans.append(s)
        return ans
```

题目要求返回数组 $\textit{answer}$，对于每个 $1 \le i \le n$，$\textit{answer}[i]$为 i 的转化。注意下标 i 从 1 开始。

根据题目描述，当 i 是 3 的倍数时 $\textit{answer}[i]$ 包含 $\text{"Fizz"}$，当 i 是 5 的倍数时 $\textit{answer}[i]$ 包含 $\text{“Buzz"}$，当 i 同时是 3 的倍数和 5 的倍数时 $\textit{answer}[i]$ 既包含 $\text{“Fizz"}$ 也包含 $\text{“Fuzz"}$，且 $\text{“Fizz"}$ 在$ \text{"Buzz"}$ 前面。

基于上述规则，对于每个 $1 \le i \le n$，可以使用字符串拼接的方式得到 $\textit{answer}[i]$，具体操作如下：

1. 如果 i 是 3 的倍数，则将 $\text{"Fizz"}$ 拼接到 $\textit{answer}[i]$；

2. 如果 i 是 5 的倍数，则将 $\text{"Buzz"}$拼接到 $\textit{answer}[i]$；

3. 如果 $\textit{answer}[i]$ 为空，则 i 既不是 3 的倍数也不是 5 的倍数，将 i 拼接到 $\textit{answer}[i]$。


#### 复杂度分析

- 时间复杂度：O(n)。需要遍历从 1 到 n 的每个整数，对于每个整数 i，生成 $\textit{answer}[i]$ 的时间复杂度是 O(1)。

- 空间复杂度：O(1)。注意返回值不计入空间复杂度。


