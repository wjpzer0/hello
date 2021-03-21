# 题目

给你一个正整数 n ，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 matrix 。

示例 1：

![img](https://assets.leetcode.com/uploads/2020/11/13/spiraln.jpg)

```
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```


示例 2：

```
输入：n = 1
输出：[[1]]
```


提示：

1 <= n <= 20

## 我的解法

参考TOP_54的解法，对该解法仍然不太熟悉。

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        ans = [[0] * n for i in range(n)]
        top = 0
        down = n - 1
        left = 0
        right = n -1
        num = 1
        while top <= down or left <= right:
            for i in range(left, right+1):
                ans[top][i] = num
                num += 1
            top += 1

            for i in range(top, down+1):
                ans[i][right] = num
                num += 1
            right -= 1

            for i in range(right, left-1, -1):
                ans[down][i] = num
                num += 1
            down -= 1

            for i in range(down, top-1, -1):
                ans[i][left] = num
                num += 1
            left += 1

        return ans
```

## 其他解法

### 模拟法

```python
class Solution:
    def generateMatrix(self, n: int) -> [[int]]:
        l, r, t, b = 0, n - 1, 0, n - 1
        mat = [[0 for _ in range(n)] for _ in range(n)]
        num, tar = 1, n * n
        while num <= tar:
            for i in range(l, r + 1): # left to right
                mat[t][i] = num
                num += 1
            t += 1
            for i in range(t, b + 1): # top to bottom
                mat[i][r] = num
                num += 1
            r -= 1
            for i in range(r, l - 1, -1): # right to left
                mat[b][i] = num
                num += 1
            b -= 1
            for i in range(b, t - 1, -1): # bottom to top
                mat[i][l] = num
                num += 1
            l += 1
        return mat
```

#### 思路：

- 生成一个 n×n 空矩阵 mat，随后模拟整个向内环绕的填入过程
  - 定义当前左右上下边界 l,r,t,b，初始值 num = 1，迭代终止值 tar = n * n；
  - 当 num <= tar 时，始终按照 从左到右 从上到下 从右到左 从下到上 填入顺序循环，每次填入后：
    - 执行 num += 1：得到下一个需要填入的数字；
    - 更新边界：例如从左到右填完后，上边界 t += 1，相当于上边界向内缩 1。
  - 使用num <= tar而不是l < r || t < b作为迭代条件，是为了解决当n为奇数时，矩阵中心数字无法在迭代过程中被填充的问题。
- 最终返回 mat 即可。

![Picture1.png](https://pic.leetcode-cn.com/ccff416fa39887c938d36fec8e490e1861813d3bba7836eda941426f13420759-Picture1.png)

### 模拟

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        dirs = [(0, 1), [1, 0], (0, -1), (-1, 0)]
        matrix = [[0] * n for _ in range(n)]
        row, col, dirIdx = 0, 0, 0
        for i in range(n * n):
            matrix[row][col] = i + 1
            dx, dy = dirs[dirIdx]
            r, c = row + dx, col + dy
            if r < 0 or r >= n or c < 0 or c >= n or matrix[r][c] > 0:
                dirIdx = (dirIdx + 1) % 4   # 顺时针旋转至下一个方向
                dx, dy = dirs[dirIdx]
            row, col = row + dx, col + dy
        
        return matrix
```

模拟矩阵的生成。按照要求，初始位置设为矩阵的左上角，初始方向设为向右。若下一步的位置超出矩阵边界，或者是之前访问过的位置，则顺时针旋转，进入下一个方向。如此反复直至填入 $n^2$个元素。

记 matrix 为生成的矩阵，其初始元素设为 0。由于填入的元素均为正数，我们可以判断当前位置的元素值，若不为 0，则说明已经访问过此位置。

#### 复杂度分析

- 时间复杂度：$O(n^2)$，其中 n 是给定的正整数。矩阵的大小是 n×n，需要填入矩阵中的每个元素。

- 空间复杂度：O(1)。除了返回的矩阵以外，空间复杂度是常数。


### 按层模拟

```python
class Solution:
    def generateMatrix(self, n: int) -> List[List[int]]:
        matrix = [[0] * n for _ in range(n)]
        num = 1
        left, right, top, bottom = 0, n - 1, 0, n - 1

        while left <= right and top <= bottom:
            for col in range(left, right + 1):
                matrix[top][col] = num
                num += 1
            for row in range(top + 1, bottom + 1):
                matrix[row][right] = num
                num += 1
            if left < right and top < bottom:
                for col in range(right - 1, left, -1):
                    matrix[bottom][col] = num
                    num += 1
                for row in range(bottom, top, -1):
                    matrix[row][left] = num
                    num += 1
            left += 1
            right -= 1
            top += 1
            bottom -= 1

        return matrix
```

可以将矩阵看成若干层，首先填入矩阵最外层的元素，其次填入矩阵次外层的元素，直到填入矩阵最内层的元素。

定义矩阵的第 k 层是到最近边界距离为 k 的所有顶点。例如，下图矩阵最外层元素都是第 1 层，次外层元素都是第 2 层，最内层元素都是第 3 层。

```
[[1, 1, 1, 1, 1, 1],
 [1, 2, 2, 2, 2, 1],
 [1, 2, 3, 3, 2, 1],
 [1, 2, 3, 3, 2, 1],
 [1, 2, 2, 2, 2, 1],
 [1, 1, 1, 1, 1, 1]]
```


对于每层，从左上方开始以顺时针的顺序填入所有元素。假设当前层的左上角位于 (top,left)，右下角位于 (bottom,right)，按照如下顺序填入当前层的元素。

从左到右填入上侧元素，依次为(top,left) 到(top,right)。

从上到下填入右侧元素，依次为 (top+1,right) 到 (bottom,right)。

如果 left<right 且 top<bottom，则从右到左填入下侧元素，依次为(bottom,right−1) 到(bottom,left+1)，以及从下到上填入左侧元素，依次为 (bottom,left) 到(top+1,left)。

填完当前层的元素之后，将 left 和 top 分别增加 1，将 right 和 bottom 分别减少 1，进入下一层继续填入元素，直到填完所有元素为止。

![img](https://assets.leetcode-cn.com/solution-static/59/1.png)

#### 复杂度分析

时间复杂度：$O(n^2)$，其中 n 是给定的正整数。矩阵的大小是 n×n，需要填入矩阵中的每个元素。

空间复杂度：O(1)。除了返回的矩阵以外，空间复杂度是常数。

