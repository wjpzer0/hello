# 题目

给出集合 [1,2,3,...,n]，其所有元素共有 n! 种排列。

按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：

"123"
"132"
"213"
"231"
"312"
"321"
给定 n 和 k，返回第 k 个排列。

示例 1：

```
输入：n = 3, k = 3
输出："213"
```


示例 2：

```
输入：n = 4, k = 9
输出："2314"
```


示例 3：

```
输入：n = 3, k = 1
输出："123"
```


提示：

- 1 <= n <= 9
- 1 <= k <= n!

## 我都解法

```python
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        def cheack(n):
            num = 1
            for i in range(1, n):
                num *= i
            return num

        k -= 1
        Permutation = ""
        listNum = [str(i) for i in range(1, n+1)]
        for i in range(n, 0, -1):
            divisor = cheack(i)
            num = k // divisor
            k = k % divisor
            Permutation = Permutation + listNum[num]
            del listNum[num]
        return Permutation
```

代码纠错的时间太长，没有很好的完善细节。

## 其他解法

### 数学 + 缩小问题规模

#### 思路

要想解决本题，首先需要了解一个简单的结论：

> 对于 n 个不同的元素（例如数 1,2,⋯,n），它们可以组成的排列总数目为 n!。
>

对于给定的 n 和 k，我们不妨从左往右确定第 k 个排列中的每一个位置上的元素到底是什么。

我们首先确定排列中的首个元素 $a_1$。根据上述的结论，我们可以知道：

- 以 1 为 a_1的排列一共有 (n-1)! 个；
- 以 2 为 a_1的排列一共有 (n-1)! 个；
- $\cdots⋯$
- 以 n 为 a_1的排列一共有 (n-1)! 个。

由于我们需要求出从小到大的第 k 个排列，因此：

- 如果 k≤(n−1)!，我们就可以确定排列的首个元素为 1；
- 如果(n−1)!<k≤2⋅(n−1)!，我们就可以确定排列的首个元素为 2；
- $\cdots⋯$
- 如果 (n−1)⋅(n−1)!<k≤n⋅(n−1)!，我们就可以确定排列的首个元素为 n。

因此，第 k 个排列的首个元素就是：
$$
a_1 = \lfloor \frac{k-1}{(n-1)!} \rfloor + 1
$$
其中 ⌊x⌋ 表示将 x 向下取整。

当我们确定了$ a_1$后，如何使用相似的思路，确定下一个元素 $a_2$呢？实际上，我们考虑以 $a_1$为首个元素的所有排列：

- 以 $[1,n] \backslash a_1$最小的元素为 $a_2$的排列一共有 (n-2)! 个；
- 以 $[1,n] \backslash a_1$次小的元素为 $a_2$的排列一共有 (n-2)! 个；
- $\cdots⋯$
- 以 $[1,n] \backslash a_1$最大的元素为 $a_2$的排列一共有 (n-2)! 个；

其中 $[1,n] \backslash a_1$表示包含 1,2,⋯n 中除去 $a_1$以外元素的集合。这些排列从编号$ (a_1-1) \cdot (n-1)!$开始，到 $a_1 \cdot (n-1)!$ 结束，总计 (n-1)! 个，因此第 k 个排列实际上就对应着这其中的第
$$
k' = (k-1) \bmod (n-1)! + 1
$$
个排列。这样一来，我们就把原问题转化成了一个完全相同但规模减少 1 的子问题：

> 求 $[1, n] \backslash a_1$这 n-1n−1 个元素组成的排列中，第 k'小的排列。
>

#### 算法

设第 k 个排列为 $a_1, a_2, \cdots, a_n$，我们从左往右地确定每一个元素 $a_i$。我们用数组 valid 记录每一个元素是否被使用过。

我们从小到大枚举 i：

- 我们已经使用过了 i-1 个元素，剩余 n-i+1 个元素未使用过，每一个元素作为 a_i都对应着 (n-i)! 个排列，总计 (n-i+1)! 个排列；

- 因此在第 k 个排列中，$a_i$即为剩余未使用过的元素中第 $\lfloor \frac{k-1}{(n-i)!} \rfloor + 1$ 小的元素；

- 在确定了 $a_i$后，这 n-i+1 个元素的第 k 个排列，就等于 $a_i$之后跟着剩余 n-i 个元素的第mod(n−i)!+1 个排列。


在实际的代码中，我们可以首先将 k 的值减少 1，这样可以减少运算，降低代码出错的概率。对应上述的后两步，即为：

因此在第 k 个排列中，$a_i$即为剩余未使用过的元素中第 $\lfloor \frac{k}{(n-i)!} \rfloor + 1$小的元素；

在确定了 $a_i$后，这 n-i+1 个元素的第 k 个排列，就等于 $a_i$之后跟着剩余 n-i 个元素的第 k mod (n−i)! 个排列。

实际上，这相当于我们将所有的排列从 0 开始进行编号。

#### 复杂度分析

- 时间复杂度：O(n^2)。
- 空间复杂度：O(n)。

#### 思考题

对于给定的排列 $a_1, a_2, \cdots, a_n$，你能求出 k 吗？

解答：

$$
k = \left( \sum_{i=1}^n \textit{order}(a_i) \cdot (n-i)! \right) + 1
$$
其中 $\textit{order}(a_i)$表示 $a_{i+1}, \cdots, a_n$中小于 $a_i$ 的元素个数。

### 有序数组（链表）模拟

```python
import java.util.LinkedList;
import java.util.List;

public class Solution {

    public String getPermutation(int n, int k) {
        // 注意：相当于在 n 个数字的全排列中找到下标为 k - 1 的那个数，因此 k 先减 1
        k --;

        int[] factorial = new int[n];
        factorial[0] = 1;
        // 先算出所有的阶乘值
        for (int i = 1; i < n; i++) {
            factorial[i] = factorial[i - 1] * i;
        }

        // 这里使用数组或者链表都行
        List<Integer> nums = new LinkedList<>();
        for (int i = 1; i <= n; i++) {
            nums.add(i);
        }

        StringBuilder stringBuilder = new StringBuilder();

        // i 表示剩余的数字个数，初始化为 n - 1
        for (int i = n - 1; i >= 0; i--) {
            int index = k / factorial[i] ;
            stringBuilder.append(nums.remove(index));
            k -= index * factorial[i];
        }
        return stringBuilder.toString();
    }
}
```

思路分析：以 n = 4，k = 6，为例，现在确定第 1 个数字填啥。如果第 k 个数恰好是后面的数字个数的阶乘，那么第 1 个数字就只能填最小的 1。

![image.png](https://pic.leetcode-cn.com/1599253484-ifkmmp-image.png)
如果 `n = 4`，`k = 16`，现在确定第 1 个数字填啥。如果 `k` > 后面的数字个数的阶乘。数一数，可以跳过几个阶乘数。

![image.png](https://pic.leetcode-cn.com/1599253676-wXGsiK-image.png)

其实这个思路很像方法一的「剪枝」，只不过方法一就减法，方法二用除法。事实上，方法二要维护数组的有序性，所以时间复杂度不变。根据以上思路，设计算法流程如下：

#### 算法流程设计

- 把候选数放在一个 有序列表 里，从左到右根据「剩下的数的阶乘数」确定每一位填谁，公式 k / (后面几位的阶乘数) 的值 恰好等于候选数组的下标；
- 选出一个数以后，k 就需要减去相应跳过的阶乘数的倍数；
- 已经填好的数需要从候选列表里删除，注意保持列表的有序性（因为排列的定义是按照字典序）；
- 由于这里考虑的是下标，第 k 个数，下标为 k - 1，一开始的时候，k--。

每次选出一个数，就将这个数从列表里面拿出。这个列表需要支持频繁的删除操作，因此使用双链表。在 Java 中 LinkedList 就是使用双链表实现的。

下面看算法是如何在示例 2 上工作的：

![image.png](https://pic.leetcode-cn.com/1599280643-QsCihG-image.png)

#### 复杂度分析

- 时间复杂度：O(N^2)，这里 N 是数组的长度；
- 空间复杂度：O(N)。

### 回溯搜索算法 + 剪枝 ，直接来到叶子结点

```python
class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        def dfs(n, k, index, path):
            if index == n:
                return
            cnt = factorial[n - 1 - index]
            for i in range(1, n + 1):
                if used[i]:
                    continue
                if cnt < k:
                    k -= cnt
                    continue
                path.append(i)
                used[i] = True
                dfs(n, k, index + 1, path)
                # 注意：这里要加 return，后面的数没有必要遍历去尝试了
                return

        if n == 0:
            return ""

        used = [False for _ in range(n + 1)]
        path = []
        factorial = [1 for _ in range(n + 1)]
        for i in range(2, n + 1):
            factorial[i] = factorial[i - 1] * i

        dfs(n, k, 0, path)
        return ''.join([str(num) for num in path])
```

思路分析：容易想到，使用同「力扣」第 TOP_46 题： 全排列 的回溯搜索算法，依次得到全排列，输出第 kk 个全排列即可。事实上，我们不必求出所有的全排列。

基于以下几点考虑：

- 所求排列 一定在叶子结点处得到，进入每一个分支，可以根据已经选定的数的个数，进而计算还未选定的数的个数，然后计算阶乘，就知道这一个分支的 叶子结点 的个数：
	- 如果 k 大于这一个分支将要产生的叶子结点数，直接跳过这个分支，这个操作叫「剪枝」；
	- 如果 k 小于等于这一个分支将要产生的叶子结点数，那说明所求的全排列一定在这一个分支将要产生的叶子结点里，需要递归求解。

![image.png](https://pic.leetcode-cn.com/1599273370-WyOYCO-image.png)

下面以示例 2：输入: n = 4，k = 9，介绍如何使用「回溯 + 剪枝」的思想得到输出 `"2314"`。

![img](https://pic.leetcode-cn.com/9dd41a79025d44e54d0ec2c8bfe5f0fb66fa2f33ee314738c7c8633ece76659f-60-3.png)

![img](https://pic.leetcode-cn.com/ae9f57c21c8069c5b92d71fbb586c3c2cf76bfd4aaf70b3d9c2365ba18cb386c-60-4.png)

![img](https://pic.leetcode-cn.com/c8127aac3ea6098c1a9ae30e9fdb212c01c48a2392301ae0d1ccc8deaadfa1ea-60-5.png)

![img](https://pic.leetcode-cn.com/0fc34901e80c13e0fcc2c0573d73404c15755cd197a7ec217bc9e5a3313f4771-60-6.png)

![img](https://pic.leetcode-cn.com/ad562087fb3503b3dbd9fb1af0635b2b8158b5113de68ea0d625d3504fa9ef78-60-7.png)

![img](https://pic.leetcode-cn.com/e3c699a081fabc9f7470614e455c92f870ac5b1808e6898c7ee086103dac0e4b-60-8.png)

![img](https://pic.leetcode-cn.com/dfaa42fe610ce291fe03e6dfe2871504621c872eab4bad2e741c58a88b0eebd4-60-9.png)

#### 编码注意事项：

计算阶乘的时候，可以使用循环计算。注意：0!=1，它表示了没有数可选的时候，即表示到达叶子结点了，排列数只剩下 1 个；
题目中说「给定 n 的范围是 [1, 9]」，可以把从 0 到 9 的阶乘计算好，放在一个数组里，可以根据索引直接获得阶乘值；
编码的时候，+1 还是 -1 ，大于还是大于等于，这些不能靠猜。常见的做法是：代入一个具体的数值，认真调试。

#### 复杂度分析：

- 时间复杂度：O(N^2)；
- 空间复杂度：O(N)，nums、used、path 都与 N 等长，factorial 数组就 10 个数，是常数级别的。

`k -= cnt;` 这一步，每一次剪枝太慢，事实上，可以用除法加快这一步骤。但是需要维护一个有序数组（或者链表），时间复杂度不变。