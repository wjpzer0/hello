#### 题目

编写一个程序，通过填充空格来解决数独问题。

一个数独的解法需遵循如下规则：

数字 1-9 在每一行只能出现一次。
数字 1-9 在每一列只能出现一次。
数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
空白格用 '.' 表示。

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/3/31/Sudoku-by-L2G-20050714_solution.svg/250px-Sudoku-by-L2G-20050714_solution.svg.png)

答案被标成红色。

提示：

给定的数独序列只包含数字 1-9 和字符 '.' 。
你可以假设给定的数独只有唯一解。
给定数独永远是 9x9 形式的。

#### 我的解法

无，有思路但是写不出算法

#### 其他解法

###### set+回溯

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        """
        Do not return anything, modify board in-place instead.
        """
        row = [set(range(1, 10)) for _ in range(9)]  # 行剩余可用数字
        col = [set(range(1, 10)) for _ in range(9)]  # 列剩余可用数字
        block = [set(range(1, 10)) for _ in range(9)]  # 块剩余可用数字

        empty = []  # 收集需填数位置
        for i in range(9):
            for j in range(9):
                if board[i][j] != '.':  # 更新可用数字
                    val = int(board[i][j])
                    row[i].remove(val)
                    col[j].remove(val)
                    block[(i // 3)*3 + j // 3].remove(val)
                else:
                    empty.append((i, j))

        def backtrack(iter=0):
            if iter == len(empty):  # 处理完empty代表找到了答案
                return True
            i, j = empty[iter]
            b = (i // 3)*3 + j // 3
            for val in row[i] & col[j] & block[b]:
                row[i].remove(val)
                col[j].remove(val)
                block[b].remove(val)
                board[i][j] = str(val)
                if backtrack(iter+1):
                    return True
                row[i].add(val)  # 回溯
                col[j].add(val)
                block[b].add(val)
            return False
        backtrack()
```

###### 位运算优化

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        def flip(i: int, j: int, digit: int):
            line[i] ^= (1 << digit)
            column[j] ^= (1 << digit)
            block[i // 3][j // 3] ^= (1 << digit)

        def dfs(pos: int):
            nonlocal valid
            if pos == len(spaces):
                valid = True
                return
            
            i, j = spaces[pos]
            mask = ~(line[i] | column[j] | block[i // 3][j // 3]) & 0x1ff
            while mask:
                digitMask = mask & (-mask)
                digit = bin(digitMask).count("0") - 1
                flip(i, j, digit)
                board[i][j] = str(digit + 1)
                dfs(pos + 1)
                flip(i, j, digit)
                mask &= (mask - 1)
                if valid:
                    return
            
        line = [0] * 9
        column = [0] * 9
        block = [[0] * 3 for _ in range(3)]
        valid = False
        spaces = list()

        for i in range(9):
            for j in range(9):
                if board[i][j] == ".":
                    spaces.append((i, j))
                else:
                    digit = int(board[i][j]) - 1
                    flip(i, j, digit)

        dfs(0)
```

**思路与算法**

在方法一中，我们使用了长度为 9 的数组表示每个数字是否出现过。我们同样也可以借助位运算，仅使用一个整数表示每个数字是否出现过。

具体地，数 b 的二进制表示的第 i位（从低到高，最低位为第 0 位）为 1，当且仅当数字 i+1 已经出现过。例如当 b 的二进制表示为 (011000100)_2 时，就表示数字 3，7，8 已经出现过。_

位运算有一些基础的使用技巧。下面列举了所有在代码中使用到的技巧：

- _对于第 i 行第 j 列的位置，line[i] | column[j] | block[i/3]\[j/3]中第 k 位为 1，表示该位置不能填入数字 k+1（因为已经出现过），其中 | 表示按位或运算。如果我们对这个值进行 ∼ 按位取反运算，那么第 k 位为 1 就表示该位置可以填入数字 k+1，我们就可以通过寻找 1 来进行枚举。由于在进行按位取反运算后，这个数的高位也全部变成了 1，而这是我们不应当枚举到的，因此我们需要将这个数和 (111111111)_2 =(1FF) _16进行按位与运算 &，将所有无关的位置为 0；
- 我们可以使用按位异或运算∧，将第 i 位从 0 变为 1，或从 1 变为 0。具体地，与数 1 << i进行按位异或运算即可，其中 << 表示左移运算；

- 我们可以用 b & (−b) 得到 b 二进制表示中最低位的 1，这是因为 (−b) 在计算机中以补码的形式存储，它等于 ∼b+1。b如果和 ∼b 进行按位与运算，那么会得到 0，但是当 ∼b 增加 1 之后，最低位的连续的 1 都变为 0，而最低位的 0 变为 1，对应到 b 中即为最低位的 1，因此当 b 和 ∼b+1 进行按位与运算时，只有最低位的 1 会被保留；

- 当我们得到这个最低位的 1 时，我们可以通过一些语言自带的函数得到这个最低位的 1 究竟是第几位（即 i 值），具体可以参考下面的代码；

- 我们可以用 b 和最低位的 1 进行按位异或运算，就可以将其从 b 中去除，这样就可以枚举下一个 1。同样地，我们也可以用 b 和 b−1 进行按位与运算达到相同的效果，读者可以自行尝试推导。


###### 枚举优化

```python
class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        def flip(i: int, j: int, digit: int):
            line[i] ^= (1 << digit)
            column[j] ^= (1 << digit)
            block[i // 3][j // 3] ^= (1 << digit)

        def dfs(pos: int):
            nonlocal valid
            if pos == len(spaces):
                valid = True
                return
            
            i, j = spaces[pos]
            mask = ~(line[i] | column[j] | block[i // 3][j // 3]) & 0x1ff
            while mask:
                digitMask = mask & (-mask)
                digit = bin(digitMask).count("0") - 1
                flip(i, j, digit)
                board[i][j] = str(digit + 1)
                dfs(pos + 1)
                flip(i, j, digit)
                mask &= (mask - 1)
                if valid:
                    return
            
        line = [0] * 9
        column = [0] * 9
        block = [[0] * 3 for _ in range(3)]
        valid = False
        spaces = list()

        for i in range(9):
            for j in range(9):
                if board[i][j] != ".":
                    digit = int(board[i][j]) - 1
                    flip(i, j, digit)
        
        while True:
            modified = False
            for i in range(9):
                for j in range(9):
                    if board[i][j] == ".":
                        mask = ~(line[i] | column[j] | block[i // 3][j // 3]) & 0x1ff
                        if not (mask & (mask - 1)):
                            digit = bin(mask).count("0") - 1
                            flip(i, j, digit)
                            board[i][j] = str(digit + 1)
                            modified = True
            if not modified:
                break
        
        for i in range(9):
            for j in range(9):
                if board[i][j] == ".":
                    spaces.append((i, j))

        dfs(0)
```

**思路与算法**

我们可以顺着位运算优化的思路继续优化下去：

如果一个空白格只有唯一的数可以填入，也就是其对应的 bb 值和 b-1 进行按位与运算后得到 0（即 b 中只有一个二进制位为 1）。此时，我们就可以确定这个空白格填入的数，而不用等到递归时再去处理它。

###### 回溯 + 状态压缩（使用 bitset）

```java
class Solution {
    private BitSet[] rows = new BitSet[9];
    private BitSet[] cols = new BitSet[9];
    private BitSet[][] cells = new BitSet[3][3];

    public void solveSudoku(char[][] board) {
        // 初始化 BitSet
        for(int i=0; i<9; i++){
            rows[i] = new BitSet(9);
            cols[i] = new BitSet(9);
            cells[i/3][i%3] = new BitSet(9);
        }

        int count = 0;
        for(int i=0; i<board.length; i++){
            for(int j=0; j<board[i].length; j++){
                char c = board[i][j];
                if(c == '.'){
                    count++;
                }else{
                    int num = c - '1';
                    rows[i].set(num);
                    cols[j].set(num);
                    cells[i/3][j/3].set(num);
                }
            }
        }

        backtrace(board, count);
    }

    private boolean backtrace(char[][] board, int count){
        if(count == 0){
            return true;
        }
        int[] pos = getNext(board);
        int x = pos[0], y = pos[1];
        BitSet okMask = getPossibleStatus(x, y);

        for(char c='1'; c<='9'; c++){
            int i = c - '1';
            if(isValid(okMask, i)){
                fillNum(x, y, i, true);
                board[x][y] = c;
                if(backtrace(board, count-1)){
                    return true;
                }
                board[x][y] = '.';
                fillNum(x, y, i, false);
            }
        }

        return false;
    }

    // num 0..8     flag == true when set num
    private void fillNum(int x, int y, int num, boolean flag){
        rows[x].set(num, flag);
        cols[y].set(num, flag);
        cells[x/3][y/3].set(num, flag);
    }

    // num 0..8
    private boolean isValid(BitSet okMask, int num){
        return okMask.get(num);
    }

    private BitSet getPossibleStatus(int x, int y){
        BitSet bitset = (BitSet)rows[x].clone();
        bitset.or(cols[y]);
        bitset.or(cells[x/3][y/3]);
        bitset.flip(0, 9);
        return bitset;
    }

    // 返回二进制中 1 的个数
    private int getOneCount(BitSet mask){
        return mask.cardinality();
    }

    // 返回限制最多(候选状态最少)的位置
    private int[] getNext(char[][] board){
        int min = 10;
        int[] res = new int[2];
        for(int i=0; i<board.length; i++){
            for(int j=0; j<board[i].length; j++){
                if(board[i][j] == '.'){
                    BitSet mask = getPossibleStatus(i, j);
                    int count = getOneCount(mask);
                    if(count < min){
                        min = count;
                        res[0] = i;
                        res[1] = j;
                    }
                }
            }
        }
        return res;
    }

}
```

**思路**
状态压缩

11. 使用 bitset<9> 来压缩存储每一行、每一列、每一个 3x3 宫格中 1-9 是否出现
12. 这样每一个格子就可以计算出所有不能填的数字，然后得到所有能填的数字 getPossibleStatus()
13. 填入数字和回溯时，只需要更新存储信息
14. 每个格子在使用时，会根据存储信息重新计算能填的数字

回溯
21. 每次都使用 getNext() 选择能填的数字最少的格子开始填，这样填错的概率最小，回溯次数也会变少
22. 使用 fillNum() 在填入和回溯时负责更新存储信息
23. 一旦全部填写成功，一路返回 true ，结束递归

图解

![图片.png](https://pic.leetcode-cn.com/1fb1c64cfddb5c66b61bd769224724a05027172d6486feb19b3a16d9473372ee-%E5%9B%BE%E7%89%87.png)

#### 知识补充

###### Java Bitset类

一个Bitset类创建一种特殊类型的数组来保存位值。BitSet中数组大小会随需要增加。这和位向量（vector of bits）比较类似。

这是一个传统的类，但它在Java 2中被完全重新设计。

BitSet定义了两个构造方法。

第一个构造方法创建一个默认的对象：

```
BitSet()
```

第二个方法允许用户指定初始大小。所有位初始化为0。

```
BitSet(int size)
```

BitSet中实现了Cloneable接口中定义的方法如下表所列：

| 序号 | 方法描述                                                     |
| :--- | :----------------------------------------------------------- |
| 1    | void and(BitSet set) 对此目标位 set 和参数位 set 执行逻辑与操作。 |
| 2    | void andNot(BitSet set) 清除此 BitSet 中所有的位，其相应的位在指定的 BitSet 中已设置。 |
| 3    | int cardinality( ) 返回此 BitSet 中设置为 true 的位数。      |
| 4    | void clear( ) 将此 BitSet 中的所有位设置为 false。           |
| 5    | void clear(int index) 将索引指定处的位设置为 false。         |
| 6    | void clear(int startIndex, int endIndex) 将指定的 startIndex（包括）到指定的 toIndex（不包括）范围内的位设置为 false。 |
| 7    | Object clone( ) 复制此 BitSet，生成一个与之相等的新 BitSet。 |
| 8    | boolean equals(Object bitSet) 将此对象与指定的对象进行比较。 |
| 9    | void flip(int index) 将指定索引处的位设置为其当前值的补码。  |
| 10   | void flip(int startIndex, int endIndex) 将指定的 fromIndex（包括）到指定的 toIndex（不包括）范围内的每个位设置为其当前值的补码。 |
| 11   | boolean get(int index) 返回指定索引处的位值。                |
| 12   | BitSet get(int startIndex, int endIndex) 返回一个新的 BitSet，它由此 BitSet 中从 fromIndex（包括）到 toIndex（不包括）范围内的位组成。 |
| 13   | int hashCode( ) 返回此位 set 的哈希码值。                    |
| 14   | boolean intersects(BitSet bitSet) 如果指定的 BitSet 中有设置为 true 的位，并且在此 BitSet 中也将其设置为 true，则返回 true。 |
| 15   | boolean isEmpty( ) 如果此 BitSet 中没有包含任何设置为 true 的位，则返回 true。 |
| 16   | int length( ) 返回此 BitSet 的"逻辑大小"：BitSet 中最高设置位的索引加 1。 |
| 17   | int nextClearBit(int startIndex) 返回第一个设置为 false 的位的索引，这发生在指定的起始索引或之后的索引上。 |
| 18   | int nextSetBit(int startIndex) 返回第一个设置为 true 的位的索引，这发生在指定的起始索引或之后的索引上。 |
| 19   | void or(BitSet bitSet) 对此位 set 和位 set 参数执行逻辑或操作。 |
| 20   | void set(int index) 将指定索引处的位设置为 true。            |
| 21   | void set(int index, boolean v)  将指定索引处的位设置为指定的值。 |
| 22   | void set(int startIndex, int endIndex) 将指定的 fromIndex（包括）到指定的 toIndex（不包括）范围内的位设置为 true。 |
| 23   | void set(int startIndex, int endIndex, boolean v) 将指定的 fromIndex（包括）到指定的 toIndex（不包括）范围内的位设置为指定的值。 |
| 24   | int size( ) 返回此 BitSet 表示位值时实际使用空间的位数。     |
| 25   | String toString( ) 返回此位 set 的字符串表示形式。           |
| 26   | void xor(BitSet bitSet) 对此位 set 和位 set 参数执行逻辑异或操作。 |

实例

```java
import java.util.BitSet;  
public class BitSetDemo {   
    public static void main(String args[]) { 
        BitSet bits1 = new BitSet(16);
        BitSet bits2 = new BitSet(16);
        
        // set some bits     
        for(int i=0; i<16; i++) { 
            if((i%2) == 0) bits1.set(i);
            if((i%5) != 0) bits2.set(i);
        }     
        System.out.println("Initial pattern in bits1: ");     
        System.out.println(bits1);     
        System.out.println("\nInitial pattern in bits2: ");    
        System.out.println(bits2); 
        
        // AND bits
        bits2.and(bits1);
        System.out.println("\nbits2 AND bits1: ");
        System.out.println(bits2);  
        
        // OR bits     
        bits2.or(bits1);     
        System.out.println("\nbits2 OR bits1: ");     
        System.out.println(bits2);      
        // XOR bits     
        bits2.xor(bits1);     
        System.out.println("\nbits2 XOR bits1: ");
        System.out.println(bits2);  
    } 
}
```

以上实例编译运行结果如下：

```
Initial pattern in bits1:
{0, 2, 4, 6, 8, 10, 12, 14}

Initial pattern in bits2:
{1, 2, 3, 4, 6, 7, 8, 9, 11, 12, 13, 14}

bits2 AND bits1:
{2, 4, 6, 8, 12, 14}

bits2 OR bits1:
{0, 2, 4, 6, 8, 10, 12, 14}

bits2 XOR bits1:
{}
```

