# 题目

给你一个字符串 `s` 表示一个学生的出勤记录，其中的每个字符用来标记当天的出勤情况（缺勤、迟到、到场）。记录中只含下面三种字符：

- `'A'`：Absent，缺勤
- `'L'`：Late，迟到
- `'P'`：Present，到场

如果学生能够 **同时** 满足下面两个条件，则可以获得出勤奖励：

- 按 **总出勤** 计，学生缺勤（`'A'`）**严格** 少于两天。
- 学生 **不会** 存在 **连续** 3 天或 3 天以上的迟到（`'L'`）记录。

如果学生可以获得出勤奖励，返回 `true` ；否则，返回 `false` 。

**示例 1：**

```
输入：s = "PPALLP"
输出：true
解释：学生缺勤次数少于 2 次，且不存在 3 天或以上的连续迟到记录。
```

**示例 2：**

```
输入：s = "PPALLL"
输出：false
解释：学生最后三天连续迟到，所以不满足出勤奖励的条件。
```

**提示：**

- `1 <= s.length <= 1000`
- `s[i]` 为 `'A'`、`'L'` 或 `'P'`

## 我的解法

```c++
class Solution {
public:
    bool checkRecord(string s) {
        if (count(s.begin(),s.end(),'A') < 2 && s.find("LLL")==string::npos)
        {
            return true;
        }
        else
        {
            return false;
        }
        // return !(count(s.begin(),s.end(),'A')>=2||s.find("LLL")!=string::npos);
    }
};
```

```python
class Solution:
    def checkRecord(self, s: str) -> bool:
        if s.count("A") < 2 and s.count("LLL") == 0:
            return True
        else:
            return False
```

内置函数永远的神！

## 其他解法

### 一次遍历

```c++
class Solution {
public:
    bool checkRecord(string s) {
        int absents = 0, lates = 0;
        for (auto &ch : s) {
            if (ch == 'A') {
                absents++;
                if (absents >= 2) {
                    return false;
                }
            }
            if (ch == 'L') {
                lates++;
                if (lates >= 3) {
                    return false;
                }
            } else {
                lates = 0;
            }
        }
        return true;
    }
};
```

```java
class Solution {
    public boolean checkRecord(String s) {
        int absents = 0, lates = 0;
        int n = s.length();
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (c == 'A') {
                absents++;
                if (absents >= 2) {
                    return false;
                }
            }
            if (c == 'L') {
                lates++;
                if (lates >= 3) {
                    return false;
                }
            } else {
                lates = 0;
            }
        }
        return true;
    }
}
```

```python
class Solution:
    def checkRecord(self, s: str) -> bool:
        absents = lates = 0
        for i, c in enumerate(s):
            if c == "A":
                absents += 1
                if absents >= 2:
                    return False
            if c == "L":
                lates += 1
                if lates >= 3:
                    return False
            else:
                lates = 0
        
        return True
```

可奖励的出勤记录要求缺勤次数少于 2 和连续迟到次数少于 3。判断出勤记录是否可奖励，只需要遍历出勤记录，判断这两个条件是否同时满足即可。

遍历过程中，记录缺勤次数和连续迟到次数，根据遍历到的字符更新缺勤次数和连续迟到次数：

如果遇到$ \text{`A'}$​，即缺勤，则将缺勤次数加 1，否则缺勤次数不变；

如果遇到 $\text{`L'}$​，即迟到，则将连续迟到次数加 1，否则将连续迟到次数清零。

如果在更新缺勤次数和连续迟到次数之后，出现缺勤次数大于或等于 2 或者连续迟到次数大于或等于 3，则该出勤记录不满足可奖励的要求，返回 $\text{false}$。如果遍历结束时未出现出勤记录不满足可奖励的要求的情况，则返回 $\text{true}$。

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是字符串 s 的长度。需要遍历字符串 s 一次。
- 空间复杂度：O(1)。



