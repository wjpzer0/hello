# 题目

给你一个有效的 [IPv4](https://baike.baidu.com/item/IPv4) 地址 `address`，返回这个 IP 地址的无效化版本。

所谓无效化 IP 地址，其实就是用 `"[.]"` 代替了每个 `"."`。

**示例 1：**

```
输入：address = "1.1.1.1"
输出："1[.]1[.]1[.]1"
```

**示例 2：**

```
输入：address = "255.100.50.0"
输出："255[.]100[.]50[.]0"
```

**提示：**

- 给出的 `address` 是一个有效的 IPv4 地址

## 我的解法

回忆内置函数！

```python
class Solution:
    def defangIPaddr(self, address: str) -> str:
        return address.replace(".", "[.]")
```

## 其他解法

### 直接遍历

```c++
class Solution {
public:
    string defangIPaddr(string address) {
        string ans;
        for (auto & c : address) {
            if (c == '.') {
                ans.append("[.]");
            } else {
                ans.push_back(c);
            }
        }
        return ans;
    }
};
```

```java
class Solution {
    public String defangIPaddr(String address) {
        return address.replace(".", "[.]");
    }
}
```

```python
class Solution:
    def defangIPaddr(self, address: str) -> str:
        return address.replace('.', '[.]')
```

#### 思路与算法

按照题目要求，依次将字符串 $\textit{address}$中 $\texttt{"."}$ 替换为 $\texttt{"[.]"}$ 即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为字符串 s 的长度。需要遍历一遍字符串即可。

- 空间复杂度：O(1)。除返回值外，不需要额外的存储空间。


