# 题目

n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 n ，返回所有不同的 n 皇后问题 的解决方案。

每一种解法包含一个不同的 n 皇后问题 的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

示例 1：

![img](https://assets.leetcode.com/uploads/2020/11/13/queens.jpg)

```
输入：n = 4
输出：[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
解释：如上图所示，4 皇后问题存在两个不同的解法。
```


示例 2：

```
输入：n = 1
输出：[["Q"]]
```


提示：

1 <= n <= 9
皇后彼此不能相互攻击，也就是说：任何两个皇后都不能处于同一条横行、纵行或斜线上。

## 我都解法

无，有思路，但是写不出来。

回溯的具体做法是：使用一个数组记录每行放置的皇后的列下标，依次在每一行放置一个皇后。每次新放置的皇后都不能和已经放置的皇后之间有攻击：即新放置的皇后不能和任何一个已经放置的皇后在同一列以及同一条斜线上，并更新数组中的当前行的皇后列下标。当 N个皇后都放置完毕，则找到一个可能的解。当找到一个可能的解之后，将数组转换成表示棋盘状态的列表，并将该棋盘状态的列表加入返回列表。

由于每个皇后必须位于不同列，因此已经放置的皇后所在的列不能放置别的皇后。第一个皇后有 NN 列可以选择，第二个皇后最多有 N-1 列可以选择，第三个皇后最多有 N-2 列可以选择（如果考虑到不能在同一条斜线上，可能的选择数量更少），因此所有可能的情况不会超过 N! 种，遍历这些情况的时间复杂度是 O(N!)。

为了降低总时间复杂度，每次放置皇后时需要快速判断每个位置是否可以放置皇后，显然，最理想的情况是在 O(1) 的时间内判断该位置所在的列和两条斜线上是否已经有皇后。

以下两种方法分别使用集合和位运算对皇后的放置位置进行判断，都可以在 O(1) 的时间内判断一个位置是否可以放置皇后，算法的总时间复杂度都是 O(N!)。

## 其他解法

### 回溯算法（转换成全排列问题 + 剪枝）

#### 方法：回溯搜索算法（深度优先遍历）

如何记住已经摆放的皇后的位置上做文章，代码结构都一样，大家只看不同的部分就可以了。

**参考代码 1**：

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    private int n;
    /**
     * 记录某一列是否放置了皇后
     */
    private boolean[] col;
    /**
     * 记录主对角线上的单元格是否放置了皇后
     */
    private boolean[] main;
    /**
     * 记录了副对角线上的单元格是否放置了皇后
     */
    private boolean[] sub;
    private List<List<String>> res;

    public List<List<String>> solveNQueens(int n) {
        res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        // 设置成员变量，减少参数传递，具体作为方法参数还是作为成员变量，请参考团队开发规范
        this.n = n;
        this.col = new boolean[n];
        this.main = new boolean[2 * n - 1];
        this.sub = new boolean[2 * n - 1];
        Deque<Integer> path = new ArrayDeque<>();
        dfs(0, path);
        return res;
    }

    private void dfs(int row, Deque<Integer> path) {
        if (row == n) {
            // 深度优先遍历到下标为 n，表示 [0.. n - 1] 已经填完，得到了一个结果
            List<String> board = convert2board(path);
            res.add(board);
            return;
        }

        // 针对下标为 row 的每一列，尝试是否可以放置
        for (int j = 0; j < n; j++) {
            if (!col[j] && !main[row - j + n - 1] && !sub[row + j]) {
                path.addLast(j);
                col[j] = true;
                main[row - j + n - 1] = true;
                sub[row + j] = true;


                dfs(row + 1, path);
                sub[row + j] = false;
                main[row - j + n - 1] = false;
                col[j] = false;
                path.removeLast();
            }
        }
    }

    private List<String> convert2board(Deque<Integer> path) {
        List<String> board = new ArrayList<>();
        for (Integer num : path) {
            StringBuilder row = new StringBuilder();
            row.append(".".repeat(Math.max(0, n)));
            row.replace(num, num + 1, "Q");
            board.add(row.toString());
        }
        return board;
    }
}
```

**参考代码 2**

其实已经摆放皇后的列下标、占据了哪一条主对角线、哪一条副对角线也可以使用哈希表来记录。

实际上哈希表底层也是数组，使用哈希表可以不用处理已经占据位置的皇后的主对角线、副对角线的下标偏移问题。

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class Solution {

    private Set<Integer> col;
    private Set<Integer> main;
    private Set<Integer> sub;
    private int n;
    private List<List<String>> res;

    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        col = new HashSet<>();
        main = new HashSet<>();
        sub = new HashSet<>();

        Deque<Integer> path = new ArrayDeque<>();
        dfs(0, path);
        return res;
    }

    private void dfs(int row, Deque<Integer> path) {
        if (row == n) {
            List<String> board = convert2board(path);
            res.add(board);
            return;
        }

        // 针对每一列，尝试是否可以放置
        for (int i = 0; i < n; i++) {
            if (!col.contains(i) && !main.contains(row - i) && !sub.contains(row + i)) {
                path.addLast(i);
                col.add(i);
                main.add(row - i);
                sub.add(row + i);

                dfs(row + 1, path);

                sub.remove(row + i);
                main.remove(row - i);
                col.remove(i);
                path.removeLast();
            }
        }
    }

    private List<String> convert2board(Deque<Integer> path) {
        List<String> board = new ArrayList<>();
        for (Integer num : path) {
            StringBuilder row = new StringBuilder();
            row.append(".".repeat(Math.max(0, n)));
            row.replace(num, num + 1, "Q");
            board.add(row.toString());
        }
        return board;
    }
}
```

**参考代码 3**

搜索问题一般来说复杂度很高，因此在线测评系统的后台测试数据不会很大。因此布尔数组可以只用一个整数来代替（int 或者 long 根据情况决定），int 类型整数等价于一个 32 位布尔数组，long 类型整数等价于一个 64位布尔数组。

使用一个整数代表一个布尔数组，在比较布尔数组所有的位的值是否相等时，只需要 O(1)，并且传递参数、复制也是相对方便的。

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.List;

public class Solution {

    private List<List<String>> res;
    private int n;

    public List<List<String>> solveNQueens(int n) {
        this.n = n;
        res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        int col = 0;
        int main = 0;
        int sub = 0;
        Deque<Integer> path = new ArrayDeque<>();

        dfs(0, col, main, sub, path);
        return res;
    }

    private void dfs(int row, int col, int sub, int main, Deque<Integer> path) {
        if (row == n) {
            List<String> board = convert2board(path);
            res.add(board);
            return;
        }

        // 针对每一列，尝试是否可以放置
        for (int i = 0; i < n; i++) {
            if (((col >> i) & 1) == 0 
                    && ((main >> (row - i + n - 1)) & 1) == 0 
                    && ((sub >> (row + i)) & 1) == 0) {
                path.addLast(i);
                col ^= (1 << i);
                main ^= (1 << (row - i + n - 1));
                sub ^= (1 << (row + i));

                dfs(row + 1, col, sub, main, path);

                sub ^= (1 << (row + i));
                main ^= (1 << (row - i + n - 1));
                col ^= (1 << i);
                path.removeLast();
            }
        }
    }

    private List<String> convert2board(Deque<Integer> path) {
        List<String> board = new ArrayList<>();
        for (Integer num : path) {
            StringBuilder row = new StringBuilder();
            row.append(".".repeat(Math.max(0, n)));
            row.replace(num, num + 1, "Q");
            board.add(row.toString());
        }
        return board;
    }
}
```

**理解树形结构**

先尝试画出递归树，以 44 皇后问题为例，画出的递归树如下：

![image.png](https://pic.leetcode-cn.com/1598117469-RXhjxi-image.png)

搜索的过程蕴含了 剪枝 的思想。「剪枝」的依据是：题目中给出的 「N 皇后」 的摆放规则：1、不在同一行；2、不在同一列；3、不在同一主对角线方向上；4、不在同一副对角线方向上。

**小技巧：记住已经摆放的皇后的位置**
这里记住已经摆放的位置不能像 Flood Fill 一样，简单地使用 visited 布尔数组。放置的规则是：一行一行考虑皇后可以放置在哪一个位置上，某一行在考虑某一列是否可以放置皇后的时候，需要根据前面已经放置的皇后的位置。

由于是一行一行考虑放置皇后，摆放的这些皇后肯定不在同一行，为了避免它们在同一列，需要一个长度为 NN 的布尔数组 cols，已经放置的皇后占据的列，就需要在对应的列的位置标注为 True。

**考虑对角线（找规律）**
下面我们研究一下主对角线或者副对角线上的元素有什么特性。在每一个单元格里写下行和列的 下标。

![image.png](https://pic.leetcode-cn.com/1599142979-VEuEDb-image.png)

为了保证至少两个皇后不同时出现在 同一主对角线方向 或者 同一副对角线方向。检查策略是，只要「检测」到新摆放的「皇后」与已经摆放好的「皇后」冲突，就尝试摆放同一行的下一个位置，到行尾还不能放置皇后，就退回到上一行。

可以像全排列 used 数组那样，再为 「主对角线（Main diagonal）」 和 「副对角线（Sub diagonal）」 设置相应的 布尔数组变量，只要排定一个 「皇后」 的位置，就需要占住对应的位置。

**编码**
我们使用一个 1 到 4 的排列表示一个4×4 的棋盘，例如：

![image.png](https://pic.leetcode-cn.com/1599142434-RjIWEI-image.png)

得到一个符合要求的全排列以后，生成棋盘的代码就很简单了。

说明：

将「行状态」、「主对角线状态」、「副对角线状态」 设置为成员变量，以避免递归方法参数冗长（参考代码 2、参考代码 3 类似看待）。至于是否有必要这样做，以后在项目开发中需要遵守项目文档的规定；
Java 中 Stack 已经废弃，推荐使用 ArrayDeque，可以查阅文档。

### 基于集合的回溯

```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        def generateBoard():
            board = list()
            for i in range(n):
                row[queens[i]] = "Q"
                board.append("".join(row))
                row[queens[i]] = "."
            return board

        def backtrack(row: int):
            if row == n:
                board = generateBoard()
                solutions.append(board)
            else:
                for i in range(n):
                    if i in columns or row - i in diagonal1 or row + i in diagonal2:
                        continue
                    queens[row] = i
                    columns.add(i)
                    diagonal1.add(row - i)
                    diagonal2.add(row + i)
                    backtrack(row + 1)
                    columns.remove(i)
                    diagonal1.remove(row - i)
                    diagonal2.remove(row + i)
                    
        solutions = list()
        queens = [-1] * n
        columns = set()
        diagonal1 = set()
        diagonal2 = set()
        row = ["."] * n
        backtrack(0)
        return solutions
```

为了判断一个位置所在的列和两条斜线上是否已经有皇后，使用三个集合 $\textit{columns}$、$\textit{diagonals}_1$和$ \textit{diagonals}_2$分别记录每一列以及两个方向的每条斜线上是否有皇后。

列的表示法很直观，一共有 N 列，每一列的下标范围从 0 到 N-1，使用列的下标即可明确表示每一列。

如何表示两个方向的斜线呢？对于每个方向的斜线，需要找到斜线上的每个位置的行下标与列下标之间的关系。

方向一的斜线为从左上到右下方向，同一条斜线上的每个位置满足行下标与列下标之差相等，例如(0,0) 和 (3,3) 在同一条方向一的斜线上。因此使用行下标与列下标之差即可明确表示每一条方向一的斜线。

![fig1](https://assets.leetcode-cn.com/solution-static/51/1.png)

方向二的斜线为从右上到左下方向，同一条斜线上的每个位置满足行下标与列下标之和相等，例如 (3,0)(3,0) 和 (1,2)(1,2) 在同一条方向二的斜线上。因此使用行下标与列下标之和即可明确表示每一条方向二的斜线。

![fig2](https://assets.leetcode-cn.com/solution-static/51/2.png)

每次放置皇后时，对于每个位置判断其是否在三个集合中，如果三个集合都不包含当前位置，则当前位置是可以放置皇后的位置。

#### 复杂度分析

- 时间复杂度：O(N!)，其中 NN 是皇后数量。

- 空间复杂度：O(N)，其中 NN 是皇后数量。空间复杂度主要取决于递归调用层数、记录每行放置的皇后的列下标的数组以及三个集合，递归调用层数不会超过 NN，数组的长度为 NN，每个集合的元素个数都不会超过 N。


### 基于位运算的回溯

```python
class Solution:
    def solveNQueens(self, n: int) -> List[List[str]]:
        def generateBoard():
            board = list()
            for i in range(n):
                row[queens[i]] = "Q"
                board.append("".join(row))
                row[queens[i]] = "."
            return board

        def solve(row: int, columns: int, diagonals1: int, diagonals2: int):
            if row == n:
                board = generateBoard()
                solutions.append(board)
            else:
                availablePositions = ((1 << n) - 1) & (~(columns | diagonals1 | diagonals2))
                while availablePositions:
                    position = availablePositions & (-availablePositions)
                    availablePositions = availablePositions & (availablePositions - 1)
                    column = bin(position - 1).count("1")
                    queens[row] = column
                    solve(row + 1, columns | position, (diagonals1 | position) << 1, (diagonals2 | position) >> 1)

        solutions = list()
        queens = [-1] * n
        row = ["."] * n
        solve(0, 0, 0, 0)
        return solutions
```

方法一使用三个集合记录分别记录每一列以及两个方向的每条斜线上是否有皇后，每个集合最多包含 N 个元素，因此集合的空间复杂度是 O(N)。如果利用位运算记录皇后的信息，就可以将记录皇后信息的空间复杂度从 O(N) 降到 O(1)。

具体做法是，使用三个整数 $\textit{columns}$、$\textit{diagonals}_1$和 $\textit{diagonals}_2$分别记录每一列以及两个方向的每条斜线上是否有皇后，每个整数有 NN 个二进制位。棋盘的每一列对应每个整数的二进制表示中的一个数位，其中棋盘的最左列对应每个整数的最低二进制位，最右列对应每个整数的最高二进制位。

那么如何根据每次放置的皇后更新三个整数的值呢？在说具体的计算方法之前，首先说一个例子。

棋盘的边长和皇后的数量 N=8N=8。如果棋盘的前两行分别在第 22 列和第 44 列放置了皇后（下标从 00 开始），则棋盘的前两行如下图所示。

![fig3](https://assets.leetcode-cn.com/solution-static/51/3.png)

如果要在下一行放置皇后，哪些位置不能放置呢？我们用 0 代表可以放置皇后的位置，1 代表不能放置皇后的位置。

新放置的皇后不能和任何一个已经放置的皇后在同一列，因此不能放置在第 2 列和第 4 列，对应 $\textit{columns}=00010100_{(2)}$。

新放置的皇后不能和任何一个已经放置的皇后在同一条方向一（从左上到右下方向）的斜线上，因此不能放置在第 4 列和第 5 列，对应 $\textit{diagonals}_1=00110000_{(2)}$。其中，第 44 列为其前两行的第 22 列的皇后往右下移动两步的位置，第 55 列为其前一行的第 44 列的皇后往右下移动一步的位置。

新放置的皇后不能和任何一个已经放置的皇后在同一条方向二（从右上到左下方向）的斜线上，因此不能放置在第 00 列和第 3 列，对应 $\textit{diagonals}_2=00001001_{(2)}$。其中，第 0 列为其前两行的第 2 列的皇后往左下移动两步的位置，第 3 列为其前一行的第 4 列的皇后往左下移动一步的位置。

![fig4](https://assets.leetcode-cn.com/solution-static/51/4.png)

由此可以得到三个整数的计算方法：

- 初始时，三个整数的值都等于 00，表示没有放置任何皇后；

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


