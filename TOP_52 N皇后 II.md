# 题目

n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 n ，返回 n 皇后问题 不同的解决方案的数量。

示例 1：

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```
输入：n = 4
输出：2
解释：如上图所示，4 皇后问题存在两个不同的解法。
```


示例 2：

```
输入：n = 1
输出：1
```


提示：

1 <= n <= 9
皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

## 我都解法

```python
class Solution:
    def totalNQueens(self, n: int) -> int:
        def solve(row: int, columns: int, diagonals1: int, diagonals2: int):
            num = 0
            if row == n:
                num += 1
            else:
                availablePositions = ((1 << n) - 1) & (~(columns | diagonals1 | diagonals2))
                while availablePositions:
                    position = availablePositions & (-availablePositions)
                    availablePositions = availablePositions & (availablePositions - 1)
                    column = bin(position - 1).count("1")
                    num = num + solve(row + 1, columns | position, (diagonals1 | position) << 1, (diagonals2 | position) >> 1)
            return num

        num = solve(0, 0, 0, 0)
        return num
```

看着TOP_52. N皇后的解法写的，无法独立写出。

### 其他解法

### 基于集合的回溯

```python
class Solution:
    def totalNQueens(self, n: int) -> int:
        def backtrack(row: int) -> int:
            if row == n:
                return 1
            else:
                count = 0
                for i in range(n):
                    if i in columns or row - i in diagonal1 or row + i in diagonal2:
                        continue
                    columns.add(i)
                    diagonal1.add(row - i)
                    diagonal2.add(row + i)
                    count += backtrack(row + 1)
                    columns.remove(i)
                    diagonal1.remove(row - i)
                    diagonal2.remove(row + i)
                return count
                    
        columns = set()
        diagonal1 = set()
        diagonal2 = set()
        return backtrack(0)
```

为了判断一个位置所在的列和两条斜线上是否已经有皇后，使用三个集合 $\textit{columns}$、$\textit{diagonals}_1$和 $\textit{diagonals}_2$分别记录每一列以及两个方向的每条斜线上是否有皇后。

列的表示法很直观，一共有 N 列，每一列的下标范围从 0 到 N-1，使用列的下标即可明确表示每一列。

如何表示两个方向的斜线呢？对于每个方向的斜线，需要找到斜线上的每个位置的行下标与列下标之间的关系。

方向一的斜线为从左上到右下方向，同一条斜线上的每个位置满足行下标与列下标之差相等，例如 (0,0)和 (3,3) 在同一条方向一的斜线上。因此使用行下标与列下标之差即可明确表示每一条方向一的斜线。

![fig1](https://assets.leetcode-cn.com/solution-static/52/1.png)

方向二的斜线为从右上到左下方向，同一条斜线上的每个位置满足行下标与列下标之和相等，例如 (3,0)(3,0) 和 (1,2)(1,2) 在同一条方向二的斜线上。因此使用行下标与列下标之和即可明确表示每一条方向二的斜线。

![fig2](https://assets.leetcode-cn.com/solution-static/52/2.png)

每次放置皇后时，对于每个位置判断其是否在三个集合中，如果三个集合都不包含当前位置，则当前位置是可以放置皇后的位置。

#### 复杂度分析

- 时间复杂度：O(N!)，其中 N 是皇后数量。

- 空间复杂度：O(N)，其中 N 是皇后数量。空间复杂度主要取决于递归调用层数以及三个集合，递归调用层数不会超过 N，每个集合的元素个数都不会超过 N。



### 基于位运算的回溯

```python
class Solution:
    def totalNQueens(self, n: int) -> int:
        def solve(row: int, columns: int, diagonals1: int, diagonals2: int) -> int:
            if row == n:
                return 1
            else:
                count = 0
                availablePositions = ((1 << n) - 1) & (~(columns | diagonals1 | diagonals2))
                while availablePositions:
                    position = availablePositions & (-availablePositions)
                    availablePositions = availablePositions & (availablePositions - 1)
                    count += solve(row + 1, columns | position, (diagonals1 | position) << 1, (diagonals2 | position) >> 1)
                return count

        return solve(0, 0, 0, 0)
```

方法一使用三个集合记录分别记录每一列以及两个方向的每条斜线上是否有皇后，每个集合最多包含 N 个元素，因此集合的空间复杂度是 O(N)。如果利用位运算记录皇后的信息，就可以将记录皇后信息的空间复杂度从 O(N) 降到 O(1)。

具体做法是，使用三个整数 $\textit{columns}$、$\textit{diagonals}_1$和 $\textit{diagonals}_2$分别记录每一列以及两个方向的每条斜线上是否有皇后，每个整数有 N 个二进制位。棋盘的每一列对应每个整数的二进制表示中的一个数位，其中棋盘的最左列对应每个整数的最低二进制位，最右列对应每个整数的最高二进制位。

那么如何根据每次放置的皇后更新三个整数的值呢？在说具体的计算方法之前，首先说一个例子。

棋盘的边长和皇后的数量 N=8。如果棋盘的前两行分别在第 2 列和第 4 列放置了皇后（下标从 0 开始），则棋盘的前两行如下图所示。

![fig3](https://assets.leetcode-cn.com/solution-static/51/3.png)

如果要在下一行放置皇后，哪些位置不能放置呢？我们用 0 代表可以放置皇后的位置，1 代表不能放置皇后的位置。

新放置的皇后不能和任何一个已经放置的皇后在同一列，因此不能放置在第 2 列和第 4 列，对应 $\textit{columns}=00010100_{(2)}$。

新放置的皇后不能和任何一个已经放置的皇后在同一条方向一（从左上到右下方向）的斜线上，因此不能放置在第 4 列和第 5 列，对应 $\textit{diagonals}_1=00110000_{(2)}$。其中，第 4 列为其前两行的第 2 列的皇后往右下移动两步的位置，第 5 列为其前一行的第4 列的皇后往右下移动一步的位置。

新放置的皇后不能和任何一个已经放置的皇后在同一条方向二（从右上到左下方向）的斜线上，因此不能放置在第 0 列和第 3 列，对应 $\textit{diagonals}_2=00001001_{(2)}$。其中，第 0 列为其前两行的第 2 列的皇后往左下移动两步的位置，第 3 列为其前一行的第 4 列的皇后往左下移动一步的位置。

![fig4](https://assets.leetcode-cn.com/solution-static/51/4.png)

由此可以得到三个整数的计算方法：

- 初始时，三个整数的值都等于 0，表示没有放置任何皇后；
- 在当前行放置皇后，如果皇后放置在第 i 列，则将三个整数的第 i 个二进制位（指从低到高的第 ii 个二进制位）的值设为 1；
- 进入下一行时，$\textit{columns}$的值保持不变，$\textit{diagonals}_1$左移一位，$\textit{diagonals}_2$右移一位，由于棋盘的最左列对应每个整数的最低二进制位，即每个整数的最右二进制位，因此对整数的移位操作方向和对棋盘的移位操作方向相反（对棋盘的移位操作方向是 $\textit{diagonals}_1$右移一位，$\textit{diagonals}_2$左移一位）。

![img](https://assets.leetcode-cn.com/solution-static/51/2_2.png)

![img](https://assets.leetcode-cn.com/solution-static/51/2_3.png)

![img](https://assets.leetcode-cn.com/solution-static/51/2_4.png)

![img](https://assets.leetcode-cn.com/solution-static/51/2_5.png)

![img](https://assets.leetcode-cn.com/solution-static/51/2_6.png)

每次放置皇后时，三个整数的按位或运算的结果即为不能放置皇后的位置，其余位置即为可以放置皇后的位置。可以通过 $(2^n-1)~\&~(\sim(\textit{columns} | \textit{diagonals}_1 | \textit{diagonals}_2))$得到可以放置皇后的位置（该结果的值为 11 的位置表示可以放置皇后的位置），然后遍历这些位置，尝试放置皇后并得到可能的解。

遍历可以放置皇后的位置时，可以利用以下两个按位与运算的性质：

- x & (−x) 可以获得 x 的二进制表示中的最低位的 1 的位置；
- x & (x−1) 可以将 x 的二进制表示中的最低位的 1 置成 0。

具体做法是，每次获得可以放置皇后的位置中的最低位，并将该位的值置成 0，尝试在该位置放置皇后。这样即可遍历每个可以放置皇后的位置。

#### 复杂度分析

- 时间复杂度：O(N!)，其中 N 是皇后数量。
- 空间复杂度：O(N)，其中 N 是皇后数量。由于使用位运算表示，因此存储皇后信息的空间复杂度是 O(1)，空间复杂度主要取决于递归调用层数和记录每行放置的皇后的列下标的数组，递归调用层数不会超过 N，数组的长度为 N。