#### 题目

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
注意：给定 n 是一个正整数。

示例 1：
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
3.  
示例 2：
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶

#### 我的解法

未解出，没有思路

#### 其他解法

###### 动态规划

动态规划，自底而上思考
我们发现，爬 i 层楼梯的方式数 = 爬 i-2 层楼梯的方式数 + 爬 i-1 层楼梯的方式数爬i层楼梯的方式数=爬i−2层楼梯的方式数+爬i−1层楼梯的方式数。
我们有两个 base case，结合上面的递推式，就能递推出爬 i 层楼梯的方式数。
用一个数组 dp 存放中间子问题的结果。
dp[i]：爬 i 级楼梯的方式数。从dp[0]、dp[1]出发，顺序计算，直到算出 dp[i]，就像填表格

![fig1](https://assets.leetcode-cn.com/solution-static/70/70_fig1.gif)

![image.png](https://pic.leetcode-cn.com/e60b159161ba4a32967a087fb1fc5b25356fc506f2eff197b19deb34e9053653-image.png)

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        dp = [0 for _ in range(n+1)]
        dp[0] = 1
        dp[1] = 1
        for i in range(2, n+1):
            dp[i] = dp[i-1] + dp[i-2]
        return dp[n]
```



###### 通项公式

**思路**

之前的方法我们已经讨论了 f(n)f(n) 是齐次线性递推，根据递推方程 f(n)=f(n−1)+f(n−2)，我们可以写出这样的特征方程：

x^2 = x + 1

求得 x_1 = (1+sqrt(5))/2，x_2 = (1-sqrt(5)/2，设通解为 f(n) = c_1 x_1 ^n + c_2 x_2 ^ n，代入初始条件 f(1) = 1，f(2) = 1，得 c_1 =1/sqrt(5)，c_2 = -1/sqrt(5)
，我们得到了这个递推数列的通项公式：

$$
f(n) = \frac{1}{\sqrt{5}}\left[ \left(\frac{1+\sqrt{5}}{2}\right)^{n} - \left(\frac{1-\sqrt{5}}{2}\right)^{n} \right]
$$
我们就可以通过这个公式直接求第 n 项了。

**复杂度**

- 时间复杂度：O(log n)，`pow` 方法将会用去 O*(log*n) 的时间。
- 空间复杂度：O(1)。

```python
public class Solution {
    public int climbStairs(int n) {
        double sqrt5 = Math.sqrt(5);
        double fibn = Math.pow((1 + sqrt5) / 2, n + 1) - Math.pow((1 - sqrt5) / 2, n + 1);
        return (int)(fibn / sqrt5);
    }
}
```

矩阵快速幂
思路

以上的方法适用于 nn 比较小的情况，在 nn 变大之后，O(n)O(n) 的时间复杂度会让这个算法看起来有些捉襟见肘。我们可以用「矩阵快速幂」的方法来优化这个过程。

首先我们可以构建这样一个递推关系：
$$
\left[ \begin{matrix} 1 & 1 \\ 1 & 0 \end{matrix} \right] \left[ \begin{matrix} f(n)\\ f(n - 1) \end{matrix} \right] = \left[ \begin{matrix} f(n) + f(n - 1)\\ f(n) \end{matrix} \right] = \left[ \begin{matrix} f(n + 1)\\ f(n) \end{matrix} \right]
$$

因此：
$$
\left[ \begin{matrix} f(n + 1)\\ f(n) \end{matrix} \right] = \left[ \begin{matrix} 1 & 1 \\ 1 & 0 \end{matrix} \right] ^n \left[ \begin{matrix} f(1)\\ f(0) \end{matrix} \right]
$$
令：


$$
M = \left[ \begin{matrix} 1 & 1 \\ 1 & 0 \end{matrix} \right]
$$
因此我们只要能快速计算矩阵 M 的 n 次幂，就可以得到 f(n) 的值。如果直接求取 M^n，时间复杂度是 O(n) 的，我们可以定义矩阵乘法，然后用快速幂算法来加速这里 M^n 的求取。

如何想到使用矩阵快速幂？

如果一个问题可与转化为求解一个矩阵的 n 次方的形式，那么可以用快速幂来加速计算
如果一个递归式形如 
$$
f(n) = \sum_{i = 1}^{m} a_i f(n - i)
$$
，即齐次线性递推式，我们就可以把数列的递推关系转化为矩阵的递推关系，即构造出一个矩阵的 nn 次方乘以一个列向量得到一个列向量，这个列向量中包含我们要求的 f(n)。一般情况下，形如 
$$
f(n) = \sum_{i = 1}^{m} a_i f(n - i)
$$
可以构造出这样的 m×m 的矩阵：

：
$$
\left[ \begin{matrix} a_1 & a_2 & a_3 & \cdots & a_m \\ 1 & 0 & 0 & \cdots & 0 \\ 0 & 1 & 0 & \cdots & 0 \\ 0 & 0 & 1 & \cdots & 0 \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & 0 & \cdots & 1 \\ \end{matrix} \right]
$$
那么遇到非齐次线性递推我们是不是就束手无策了呢？其实未必。有些时候我们可以把非齐次线性递推转化为其次线性递推，比如这样一个递推：
f(x) = (2x-6)c + f(x - 1) + f(x - 2) + f(x - 3)

我们以做这样的变换：

f(x) + xc = [f(x - 1) + (x - 1)c] + [f(x - 2) + (x - 2)c] + [f(x - 3) + (x - 3)c]

令 g(x) = f(x) + xcg(x)=f(x)+xc，那么我们又得到了一个齐次线性递：

g(x) = g(x - 1) + g(x - 2) + g(x - 3)

于是就可以使用矩阵快速幂求解了。当然并不是所有非齐次线性都可以化成齐次线性，我们还是要具体问题具体分析。

**复杂度**

- 时间复杂度：同快速幂，O*(log*n*)。
- 空间复杂度：O(1)。

```java
public class Solution {
    public int climbStairs(int n) {
        int[][] q = {{1, 1}, {1, 0}};
        int[][] res = pow(q, n);
        return res[0][0];
    }

    public int[][] pow(int[][] a, int n) {
        int[][] ret = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ret;
    }

    public int[][] multiply(int[][] a, int[][] b) {
        int[][] c = new int[2][2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }
}
```

