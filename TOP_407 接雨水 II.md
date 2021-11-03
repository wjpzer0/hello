# 题目

给你一个 `m x n` 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。

 

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/04/08/trap1-3d.jpg)

```
输入: heightMap = [[1,4,3,1,3,2],[3,2,1,3,2,4],[2,3,3,2,3,1]]
输出: 4
解释: 下雨后，雨水将会被上图蓝色的方块中。总的接雨水量为1+2+1=4。
```

**示例 2:**

![img](https://assets.leetcode.com/uploads/2021/04/08/trap2-3d.jpg)

```
输入: heightMap = [[3,3,3,3,3],[3,2,2,2,3],[3,2,1,2,3],[3,2,2,2,3],[3,3,3,3,3]]
输出: 10
```

 

**提示:**

- `m == heightMap.length`
- `n == heightMap[i].length`
- `1 <= m, n <= 200`
- $0 <= heightMap[i][j] <= 2 * 10^4$

##  我的解法

2D的解法都没有完全弄明白，3D的解法直接敲懵逼！

## 其他解法

### 最小堆

```c++
typedef pair<int,int> pii;

class Solution {
public:
    int trapRainWater(vector<vector<int>>& heightMap) {  
        if (heightMap.size() <= 2 || heightMap[0].size() <= 2) {
            return 0;
        }  
        int m = heightMap.size();
        int n = heightMap[0].size();
        priority_queue<pii, vector<pii>, greater<pii>> pq;
        vector<vector<bool>> visit(m, vector<bool>(n, false));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    pq.push({heightMap[i][j], i * n + j});
                    visit[i][j] = true;
                }
            }
        }

        int res = 0;
        int dirs[] = {-1, 0, 1, 0, -1};
        while (!pq.empty()) {
            pii curr = pq.top();
            pq.pop();            
            for (int k = 0; k < 4; ++k) {
                int nx = curr.second / n + dirs[k];
                int ny = curr.second % n + dirs[k + 1];
                if( nx >= 0 && nx < m && ny >= 0 && ny < n && !visit[nx][ny]) {
                    if (heightMap[nx][ny] < curr.first) {
                        res += curr.first - heightMap[nx][ny]; 
                    }
                    visit[nx][ny] = true;
                    pq.push({max(heightMap[nx][ny], curr.first), nx * n + ny});
                }
            }
        }
        
        return res;
    }
};
```

```java
class Solution {
    public int trapRainWater(int[][] heightMap) {
        if (heightMap.length <= 2 || heightMap[0].length <= 2) {
            return 0;
        }
        int m = heightMap.length;
        int n = heightMap[0].length;
        boolean[][] visit = new boolean[m][n];
        PriorityQueue<int[]> pq = new PriorityQueue<>((o1, o2) -> o1[1] - o2[1]);

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    pq.offer(new int[]{i * n + j, heightMap[i][j]});
                    visit[i][j] = true;
                }
            }
        }
        int res = 0;
        int[] dirs = {-1, 0, 1, 0, -1};
        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            for (int k = 0; k < 4; ++k) {
                int nx = curr[0] / n + dirs[k];
                int ny = curr[0] % n + dirs[k + 1];
                if (nx >= 0 && nx < m && ny >= 0 && ny < n && !visit[nx][ny]) {
                    if (curr[1] > heightMap[nx][ny]) {
                        res += curr[1] - heightMap[nx][ny];
                    }
                    pq.offer(new int[]{nx * n + ny, Math.max(heightMap[nx][ny], curr[1])});
                    visit[nx][ny] = true;
                }
            }
        }
        return res;
    }
}
```

```python
class Solution:
    def trapRainWater(self, heightMap: List[List[int]]) -> int:
        if len(heightMap) <= 2 or len(heightMap[0]) <= 2:
            return 0

        m, n = len(heightMap), len(heightMap[0])
        visited = [[0 for _ in range(n)] for _ in range(m)]
        pq = []
        for i in range(m):
            for j in range(n):
                if i == 0 or i == m - 1 or j == 0 or j == n - 1:
                    visited[i][j] = 1
                    heapq.heappush(pq, (heightMap[i][j], i * n + j))
        
        res = 0
        dirs = [-1, 0, 1, 0, -1]
        while pq:
            height, position = heapq.heappop(pq)
            for k in range(4):
                nx, ny = position // n + dirs[k], position % n + dirs[k + 1]
                if nx >= 0 and nx < m and ny >= 0 and ny < n and visited[nx][ny] == 0:
                    if height > heightMap[nx][ny]:
                        res += height - heightMap[nx][ny]
                    visited[nx][ny] = 1    
                    heapq.heappush(pq, (max(height, heightMap[nx][ny]), nx * n + ny))
        return res
```

#### 思路与算法

本题为经典题目，解题的原理和方法都可以参考 TOP_42.接雨水，本题主要从一维数组变成了二维数组。
首先我们思考一下什么样的方块一定可以接住水：

- 该方块不为最外层的方块；
- 该方块自身的高度比其上下左右四个相邻的方块接水后的高度都要低；

我们假设方块的索引为 ，方块的高度为$ \textit{heightMap}[i][j]$，方块接水后的高度为 $\textit{water}[i][j]$。则我们知道方块 $(i,j)$ 的接水后的高度为：
$$
\textit{water}[i][j] = \max(\textit{heightMap}[i],\min(\textit{water}[i-1][j],\textit{water}[i+1][j],\textit{water}[i][j-1],\textit{water}[i][j+1]))
$$


我们知道方块 $(i,j) $实际接水的容量计算公式为$ \textit{water}[i][j] - \textit{heightMap}[i][j]$[j]。
首先我们可以确定的是，矩阵的最外层的方块接水后的高度就是方块的自身高度，因为最外层的方块无法接水，因此最外层的方块 $\textit{water}[i][j] = \textit{heightMap}[i][j]$。

根据木桶原理，接到的雨水的高度由这个容器周围最短的木板来确定的。我们可以知道容器内水的高度取决于最外层高度最低的方块，如图 1 所示：

![1](https://assets.leetcode-cn.com/solution-static/407/407_1.PNG)

我们假设已经知道最外层的方块接水后的高度的最小值，则此时我们根据木桶原理，肯定可以确定最小高度方块的相邻方块的接水高度。我们同时更新最外层的方块标记，我们在新的最外层的方块再次找到接水后的高度的最小值，同时确定与其相邻的方块的接水高度，如图 2 所示:

![2](https://assets.leetcode-cn.com/solution-static/407/407_2.PNG)

然后再次更新最外层，依次迭代直到求出所有的方块的接水高度，即可知道矩阵中的接水容量。

#### 复杂度分析

- 时间复杂度：$O(MN\log(M+N))$，其中 M 是矩阵的行数，N 是矩阵的列数。我们需要将矩阵中的每个元素都进行遍历，同时将每个元素都需要插入到优先队列中，总共需要向队列中插入 MN 个元素，每次堆进行调整的时间复杂度为 $O(\log(M+N))$，因此总的时间复杂度为 $O(MN\log(M+N))$。

- 空间复杂度：O(MN)，其中 M 是矩阵的行数，N 是矩阵的列数。我们需要创建额外的空间对元素进行标记，优先队列中最多存储 O(MN)个元素，因此空间复杂度度为 O(MN)。


### 广度优先搜索

```c++
class Solution {
public:
    int trapRainWater(vector<vector<int>>& heightMap) {
        int m = heightMap.size(), n = heightMap[0].size();
        int maxHeight = 0;
        int dirs[] = {-1, 0, 1, 0, -1};

        for (int i = 0; i < m; ++i) {
            maxHeight = max(maxHeight, *max_element(heightMap[i].begin(), heightMap[i].end()));
        }
        vector<vector<int>> water(m, vector<int>(n, maxHeight));
        queue<pair<int,int>> qu;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    if (water[i][j] > heightMap[i][j]) {
                        water[i][j] = heightMap[i][j];
                        qu.push(make_pair(i, j));
                    }
                }
            }
        }        
        while (!qu.empty()) {
            int x = qu.front().first, y = qu.front().second;
            qu.pop();
            for (int i = 0; i < 4; ++i) {
                int nx = x + dirs[i], ny = y + dirs[i + 1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) {
                    continue;
                }
                if (water[x][y] < water[nx][ny] && water[nx][ny] > heightMap[nx][ny]) {
                    water[nx][ny] = max(water[x][y], heightMap[nx][ny]);
                    qu.push(make_pair(nx, ny));
                }
            }
        }

        int res = 0;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                res += water[i][j] - heightMap[i][j];
            }
        }
        return res;
    }
};
```

```java
class Solution {
    public int trapRainWater(int[][] heightMap) {
        int m = heightMap.length;
        int n = heightMap[0].length;
        int[] dirs = {-1, 0, 1, 0, -1};
        int maxHeight = 0;
        
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                maxHeight = Math.max(maxHeight, heightMap[i][j]);
            }
        }
        int[][] water = new int[m][n];
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j){
                water[i][j] = maxHeight;      
            }
        }  
        Queue<int[]> qu = new LinkedList<>();
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    if (water[i][j] > heightMap[i][j]) {
                        water[i][j] = heightMap[i][j];
                        qu.offer(new int[]{i, j});
                    }
                }
            }
        } 
        while (!qu.isEmpty()) {
            int[] curr = qu.poll();
            int x = curr[0];
            int y = curr[1];
            for (int i = 0; i < 4; ++i) {
                int nx = x + dirs[i], ny = y + dirs[i + 1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) {
                    continue;
                }
                if (water[x][y] < water[nx][ny] && water[nx][ny] > heightMap[nx][ny]) {
                    water[nx][ny] = Math.max(water[x][y], heightMap[nx][ny]);
                    qu.offer(new int[]{nx, ny});
                }
            }
        }

        int res = 0;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                res += water[i][j] - heightMap[i][j];
            }
        }
        return res;
    }
}
```

```python
class Solution:
    def trapRainWater(self, heightMap: List[List[int]]) -> int:
        m, n = len(heightMap), len(heightMap[0])
        maxHeight = max(max(row) for row in heightMap)
        water = [[maxHeight for _ in range(n)] for _ in range(m)]
        dirs = [-1, 0, 1, 0, -1]

        qu = []
        for i in range(m):
            for j in range(n):
                if i == 0 or i == m - 1 or j == 0 or j == n - 1:
                     if water[i][j] > heightMap[i][j]:
                        water[i][j] = heightMap[i][j]
                        qu.append([i, j])
        
        while len(qu) > 0:
            [x, y] = qu.pop(0)
            for i in range(4):
                nx, ny = x + dirs[i], y + dirs[i + 1]
                if nx < 0 or nx >= m or ny < 0 or ny >= n:
                    continue
                if water[x][y] < water[nx][ny] and water[nx][ny] > heightMap[nx][ny]:
                    water[nx][ny] = max(water[x][y], heightMap[nx][ny])
                    qu.append([nx, ny])

        ans = 0
        for i in range(m):
            for j in range(n):
                ans = ans + water[i][j] - heightMap[i][j]
        return ans
```

#### 思路与算法

我们假设初始时矩阵的每个格子都接满了水，且高度均为 $\textit{maxHeight}$，其中 $\textit{maxHeight}$ 为矩阵中高度最高的格子。我们知道方块接水后的高度为 $\textit{water}[i][j]$[，它的求解公式与方法一样。方块 $(i,j)$的接水后的高度为：

$$
\textit{water}[i][j] = \max(\textit{heightMap}[i],\min(\textit{water}[i-1][j],\textit{water}[i+1][j],\textit{water}[i][j-1],\textit{water}[i][j+1]))
$$
我们知道方块 $(i,j)$ 实际接水的容量计算公式为 $\textit{water}[i][j] - \textit{heightMap}[i][j]$。
我们首先假设每个方块 $(i,j)$ 的接水后的高度均为 $\textit{water}[i][j] = \textit{maxHeight}$，首先我们知道最外层的方块的肯定不能接水，所有的多余的水都会从最外层的方块溢出，我们每次发现当前方块 $(i,j)$ 的接水高度 $\textit{water}[i][j]$小于与它相邻的 4 个模块的接水高度时，则我们将进行调整接水高度，我们将其相邻的四个方块的接水高度调整与 $(i,j)$ 的高度保持一致，我们不断重复的进行调整，直到所有的方块的接水高度不再有调整时即为满足要求。

#### 复杂度分析

- 时间复杂度：$O(M^2N^2)$，其中 M 是矩阵的行数，N 是矩阵的列数。因此每次发现有水面高度需要调整时，可能需要调整整个矩阵，因此时间复杂度为 $O(M^2N^2)$。

- 空间复杂度：O(MN)，其中 M 是矩阵的行数，N 是矩阵的列数。我们需要创建额外的空间对每个元素进行标记，因此空间复杂度度为 O(MN)。


