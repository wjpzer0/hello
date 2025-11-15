#### 题目

给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

在杨辉三角中，每个数是它左上方和右上方的数的和。

示例:

```
输入: 3
输出: [1,3,3,1]
```

进阶：
你可以优化你的算法到 O(k) 空间复杂度吗？

#### 我的解法

```python
class Solution:
    def getRow(self, rowIndex: int) -> List[int]:
        if rowIndex == 0: return [1]
        listMid = [1]
        i = 1
        while i <= rowIndex:
            listRe = [a+b for a, b in zip([0]+listMid, listMid+[0])]
            listMid = listRe
            i +=1
        return listRe
```

参考TOP_118的**错一位再逐个相加**的解法

#### 其他解法

###### 公式法

```java
public List<Integer> getRow(int rowIndex) {
    List<Integer> ans = new ArrayList<>();
    int N = rowIndex;
    for (int k = 0; k <= N; k++) {
        ans.add(Combination(N, k));
    }
    return ans;
}

private int Combination(int N, int k) {
    long res = 1;
    for (int i = 1; i <= k; i++)
        res = res * (N - k + i) / i;
    return (int) res;
}
```

优化

```java
public List<Integer> getRow(int rowIndex) {
    List<Integer> ans = new ArrayList<>();
    int N = rowIndex;
    long pre = 1;
    ans.add(1);
    for (int k = 1; k <= N; k++) {
        long cur = pre * (N - k + 1) / k;
        ans.add((int) cur);
        pre = cur;
    }
    return ans;
}
```

如果熟悉杨辉三角，应该记得杨辉三角其实可以看做由组合数构成。

![img](https://pic.leetcode-cn.com/195de01eae91e09de14dd13daafbef986c42345f2bdef405153a1742175079f4.jpg)

根据组合数的公式，将(n-k)!约掉，化简就是下边的结果。

$$
C^k_n = n!/(k!(n-k)!) = (n*(n-1)*(n-2)*...(n-k+1))/k!
$$
然后我们就可以利用组合数解决这道题。

我们可以优化一下。

上边的算法对于每个组合数我们都重新求了一遍，但事实上前后的组合数其实是有联系的。

$$
C_n^k=C_n^{k-1}\times(n-k+1)/k
$$
代码的话，我们只需要用pre变量保存上一次的组合数结果。计算过程中，可能越界，所以用到了long。

**复杂度分析**

- 时间复杂度：$O(\textit{rowIndex})$。
- 空间复杂度：O(1)。不考虑返回值的空间占用。

###### 递推

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<List<Integer>> C = new ArrayList<List<Integer>>();
        for (int i = 0; i <= rowIndex; ++i) {
            List<Integer> row = new ArrayList<Integer>();
            for (int j = 0; j <= i; ++j) {
                if (j == 0 || j == i) {
                    row.add(1);
                } else {
                    row.add(C.get(i - 1).get(j - 1) + C.get(i - 1).get(j));
                }
            }
            C.add(row);
        }
        return C.get(rowIndex);
    }
}
```

**优化**

注意到对第 i+1行的计算仅用到了第 i 行的数据，因此可以使用**滚动数组**的思想优化空间复杂度。

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> pre = new ArrayList<Integer>();
        for (int i = 0; i <= rowIndex; ++i) {
            List<Integer> cur = new ArrayList<Integer>();
            for (int j = 0; j <= i; ++j) {
                if (j == 0 || j == i) {
                    cur.add(1);
                } else {
                    cur.add(pre.get(j - 1) + pre.get(j));
                }
            }
            pre = cur;
        }
        return pre;
    }
}
```

**进一步优化**

能否只用一个数组呢？

递推式$ \mathcal{C}_n^i=\mathcal{C}_{n-1}^i+\mathcal{C}_{n-1}^{i-1}$表明，当前行第 i 项的计算只与上一行第 i-1 项及第 i 项有关。因此我们可以倒着计算当前行，这样计算到第 i 项时，第 i-1 项仍然是上一行的值。

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> row = new ArrayList<Integer>();
        row.add(1);
        for (int i = 1; i <= rowIndex; ++i) {
            row.add(0);
            for (int j = i; j > 0; --j) {
                row.set(j, row.get(j) + row.get(j - 1));
            }
        }
        return row;
    }
}
```

杨辉三角，是二项式系数在三角形中的一种几何排列。它是中国古代数学的杰出研究成果之一，它把二项式系数图形化，把组合数内在的一些代数性质直观地从图形中体现出来，是一种离散型的数与形的结合。

杨辉三角具有以下性质：

- 每行数字左右对称，由 1 开始逐渐变大再变小，并最终回到 1。

- 第 n 行（从 0 开始编号）的数字有 n+1 项，前 n 行共有 $\frac{n(n+1)}{2} $个数。

- 第 n 行的第 m 个数（从 0 开始编号）可表示为可以被表示为组合数$ \mathcal{C}(n,m)$，记作 $\mathcal{C}_n^m$或 $\binom{n}{m}$，即为从 n 个不同元素中取 m 个元素的组合数。我们可以用公式来表示它：$\mathcal{C}_n^m=\dfrac{n!}{m!(n-m)!}$

- 每个数字等于上一行的左右两个数字之和，可用此性质写出整个杨辉三角。即第 n 行的第 i 个数等于第 n−1 行的第 i-1个数和第 i 个数之和。这也是组合数的性质之一，即 $\mathcal{C}_n^i=\mathcal{C}_{n-1}^i+\mathcal{C}_{n-1}^{i-1}$ 。
- $(a+b)^n$的展开式（二项式展开）中的各项系数依次对应杨辉三角的第 n 行中的每一项。

依据性质 4，我们可以一行一行地计算杨辉三角。每当我们计算出第 i 行的值，我们就可以在线性时间复杂度内计算出第 i+1 行的值。

**复杂度分析**

时间复杂度：$O(\textit{rowIndex}^2)$。

空间复杂度：O(1)。不考虑返回值的空间占用。

