# 题目

请你判断一个 `9x9` 的数独是否有效。只需要 **根据以下规则** ，验证已经填入的数字是否有效即可。

1. 数字 `1-9` 在每一行只能出现一次。
2. 数字 `1-9` 在每一列只能出现一次。
3. 数字 `1-9` 在每一个以粗实线分隔的 `3x3` 宫内只能出现一次。（请参考示例图）

数独部分空格内已填入了数字，空白格用 `'.'` 表示。

**注意：**

- 一个有效的数独（部分已被填充）不一定是可解的。
- 只需要根据以上规则，验证已经填入的数字是否有效即可。

 

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/04/12/250px-sudoku-by-l2g-20050714svg.png)

```
输入：board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
输出：true
```

**示例 2：**

```
输入：board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
输出：false
解释：除了第一行的第一个数字从 5 改为 8 以外，空格内其他数字均与 示例1 相同。 但由于位于左上角的 3x3 宫内有两个 8 存在, 因此这个数独是无效的。
```

**提示：**

- `board.length == 9`
- `board[i].length == 9`
- `board[i][j]` 是一位数字或者 `'.'`

## 我的解法

```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:

        for i in range(9):
            nums = []
            for j in range(9):
                num = board[i][j]
                if num != ".":
                    if num not in nums:
                        nums.append(num)
                    else:
                        return False

        for i in range(9):
            nums = []
            for j in range(9):
                num = board[j][i]
                if num != ".":
                    if num not in nums:
                        nums.append(num)
                    else:
                        return False      

        for i in range(9):
            i_j = i % 3
            i_i = i // 3
            nums = []
            for j in range(9):
                j_j = j % 3
                j_i = j // 3
                num = board[i_i * 3 + j_i][i_j * 3 + j_j]
                if num != ".":
                    if num not in nums:
                        nums.append(num)
                    else:
                        return False
        
        return True
```

憨批，暴力破解！

## 其他解法

```c++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        int rows[9][9];
        int columns[9][9];
        int subboxes[3][3][9];
        
        memset(rows,0,sizeof(rows));
        memset(columns,0,sizeof(columns));
        memset(subboxes,0,sizeof(subboxes));
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char c = board[i][j];
                if (c != '.') {
                    int index = c - '0' - 1;
                    rows[i][index]++;
                    columns[j][index]++;
                    subboxes[i / 3][j / 3][index]++;
                    if (rows[i][index] > 1 || columns[j][index] > 1 || subboxes[i / 3][j / 3][index] > 1) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
};
```

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        int[][] rows = new int[9][9];
        int[][] columns = new int[9][9];
        int[][][] subboxes = new int[3][3][9];
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                char c = board[i][j];
                if (c != '.') {
                    int index = c - '0' - 1;
                    rows[i][index]++;
                    columns[j][index]++;
                    subboxes[i / 3][j / 3][index]++;
                    if (rows[i][index] > 1 || columns[j][index] > 1 || subboxes[i / 3][j / 3][index] > 1) {
                        return false;
                    }
                }
            }
        }
        return true;
    }
}
```

### 一次遍历

有效的数独满足以下三个条件：

- 同一个数字在每一行只能出现一次；

- 同一个数字在每一列只能出现一次；

- 同一个数字在每一个小九宫格只能出现一次。


可以使用哈希表记录每一行、每一列和每一个小九宫格中，每个数字出现的次数。只需要遍历数独一次，在遍历的过程中更新哈希表中的计数，并判断是否满足有效的数独的条件即可。

对于数独的第 ii 行第 jj 列的单元格，其中 $0 \le i, j < 9$，该单元格所在的行下标和列下标分别为 i 和 j，该单元格所在的小九宫格的行数和列数分别为 $\Big\lfloor \dfrac{i}{3} \Big\rfloor$和 $\Big\lfloor \dfrac{j}{3} \Big\rfloor$，其中 $0 \le \Big\lfloor \dfrac{i}{3} \Big\rfloor, \Big\lfloor \dfrac{j}{3} \Big\rfloor < 3$。

由于数独中的数字范围是 1 到 9，因此可以使用数组代替哈希表进行计数。

具体做法是，创建二维数组 $\textit{rows}$ 和 $\textit{columns}$ 分别记录数独的每一行和每一列中的每个数字的出现次数，创建三维数组 $\textit{subboxes}$ 记录数独的每一个小九宫格中的每个数字的出现次数，其中 $\textit{rows}[i][\textit{index}]$、$\textit{columns}[j][\textit{index}]$ 和 $\textit{subboxes}\Big[\Big\lfloor \dfrac{i}{3} \Big\rfloor\Big]\Big[\Big\lfloor \dfrac{j}{3} \Big\rfloor\Big]\Big[\textit{index}\Big]$ 分别表示数独的第 i 行第 j 列的单元格所在的行、列和小九宫格中，数字 $\textit{index} + 1$ 出现的次数，其中 $0 \le \textit{index} < 9$，对应的数字 $\textit{index} + 1$ 满足 $1 \le \textit{index} + 1 \le 9$。

如果 $\textit{board}[i][j]$填入了数字 n，则将 $\textit{rows}[i][n - 1]$、$\textit{columns}[j][n - 1]$ 和 $\textit{subboxes}\Big[\Big\lfloor \dfrac{i}{3} \Big\rfloor\Big]\Big[\Big\lfloor \dfrac{j}{3} \Big\rfloor\Big]\Big[n - 1\Big]$ 各加 1。如果更新后的计数大于 1，则不符合有效的数独的条件，返回 $\text{false}$。

如果遍历结束之后没有出现计数大于 1 的情况，则符合有效的数独的条件，返回 $\text{true}$。

#### 复杂度分析

- 时间复杂度：O(1)。数独共有 81 个单元格，只需要对每个单元格遍历一次即可。

- 空间复杂度：O(1)。由于数独的大小固定，因此哈希表的空间也是固定的。


