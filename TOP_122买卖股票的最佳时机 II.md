#### 题目

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

示例 1:

```
输入: [7,1,5,3,6,4]
输出: 7
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
```


示例 2:

```
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```


示例 3:

```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```


提示：

1 <= prices.length <= 3 * 10 ^ 4
0 <= prices[i] <= 10 ^ 4

#### 我的解法

未解出，想到了在数值下降的时候更改最小值，但没有更好的利用这一特性

#### 其他解法

###### 贪心

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0
        for i in range(1, len(prices)):
            tmp = prices[i] - prices[i - 1]
            if tmp > 0: profit += tmp
        return profit
```

**解题思路：**
**股票买卖策略：**

- 单独交易日： 设今天价格 p_1、明天价格 p_2，则今天买入、明天卖出可赚取金额 p_2 - p_1（负值代表亏损）。
- 连续上涨交易日： 设此上涨交易日股票价格分别为 p_1, p_2, ... , p_n，则第一天买最后一天卖收益最大，即 p_n - p_1；等价于每天都买卖，即 p_n - p_1=(p_2 - p_1)+(p_3 - p_2)+...+(p_n - p_(n-1)）。
- 连续下降交易日： 则不买卖收益最大，即不会亏钱。

**算法流程：**

遍历整个股票交易日价格列表 price，策略是所有上涨交易日都买卖（赚到所有利润），所有下降交易日都不买卖（永不亏钱）。

1. 设 tmp 为第 i-1 日买入与第 i 日卖出赚取的利润，即 tmp = prices[i] - prices[i - 1] ；
2. 当该天利润为正 tmp > 0，则将利润加入总利润 profit；当利润为 00 或为负，则直接跳过；
3. 遍历完成后，返回总利润 profit。

**复杂度分析：**

- 时间复杂度 O(N) ： 只需遍历一次price；
- 空间复杂度 O(1) ： 变量使用常数额外空间。

回到贪心算法的定义：（下面是来自《算法导论（第三版）》第 16 章的叙述）

![img](https://pic.leetcode-cn.com/5de2bc50e56f42566522df6fe4dcf0eca7396db765f7a2ca2fe9f1fd9a647270.png)

> 贪心算法 在每一步总是做出在当前看来最好的选择。

- 「贪心算法」 和 「动态规划」、「回溯搜索」 算法一样，完成一件事情，是 分步决策 的；

- 「贪心算法」 在每一步总是做出在当前看来最好的选择，我是这样理解 「最好」 这两个字的意思：

  - 「最好」 的意思往往根据题目而来，可能是 「最小」，也可能是 「最大」；

  - 贪心算法和动态规划相比，它既不看前面（也就是说它不需要从前面的状态转移过来），也不看后面（无后效性，后面的选择不会对前面的选择有影响），因此贪心算法时间复杂度一般是线性的，空间复杂度是常数级别的；

- 这道题 「贪心」 的地方在于，对于 「今天的股价 - 昨天的股价」，得到的结果有 3 种可能：① 正数，② 00，③负数。贪心算法的决策是： 只加正数 。

###### 动态规划

```java
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len < 2) {
            return 0;
        }

        // 0：持有现金
        // 1：持有股票
        // 状态转移：0 → 1 → 0 → 1 → 0 → 1 → 0
        int[][] dp = new int[len][2];

        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        for (int i = 1; i < len; i++) {
            // 这两行调换顺序也是可以的
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return dp[len - 1][0];
    }
}
```

我们也可以将状态数组分开设置。

```java
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len < 2) {
            return 0;
        }

        // cash：持有现金
        // hold：持有股票
        // 状态数组
        // 状态转移：cash → hold → cash → hold → cash → hold → cash
        int[] cash = new int[len];
        int[] hold = new int[len];

        cash[0] = 0;
        hold[0] = -prices[0];

        for (int i = 1; i < len; i++) {
            // 这两行调换顺序也是可以的
            cash[i] = Math.max(cash[i - 1], hold[i - 1] + prices[i]);
            hold[i] = Math.max(hold[i - 1], cash[i - 1] - prices[i]);
        }
        return cash[len - 1];
    }
}
```

**考虑优化空间**

由于当前行只参考上一行，每一行就 2 个值，因此可以考虑使用「滚动变量」（「滚动数组」技巧）。

```
public class Solution {

    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len < 2) {
            return 0;
        }

        // cash：持有现金
        // hold：持有股票
        // 状态转移：cash → hold → cash → hold → cash → hold → cash

        int cash = 0;
        int hold = -prices[0];

        int preCash = cash;
        int preHold = hold;
        for (int i = 1; i < len; i++) {
            cash = Math.max(preCash, preHold + prices[i]);
            hold = Math.max(preHold, preCash - prices[i]);

            preCash = cash;
            preHold = hold;
        }
        return cash;
    }
}
```

**复杂度分析：**

- 时间复杂度：O(N)，这里 NN 表示股价数组的长度；
- 空间复杂度：O(1)，分别使用两个滚动变量，将一维数组状态优化到常数大小。

**第 1 步：定义状态**
状态 dp\[i][j] 定义如下：

dp\[i][j] 表示到下标为 i 的这一天，持股状态为 j 时，我们手上拥有的最大现金数。

注意：限定持股状态为 j 是为了方便推导状态转移方程，这样的做法满足 无后效性。

其中：

第一维 i 表示下标为 i 的那一天（ 具有前缀性质，即考虑了之前天数的交易 ）；
第二维 j 表示下标为 i 的那一天是持有股票，还是持有现金。这里 0 表示持有现金（cash），1 表示持有股票（stock）。
**第 2 步：思考状态转移方程**
状态从持有现金（cash）开始，到最后一天我们关心的状态依然是持有现金（cash）；
每一天状态可以转移，也可以不动。状态转移用下图表示：


（状态转移方程写在代码中）

说明：

由于不限制交易次数，除了最后一天，每一天的状态可能不变化，也可能转移；
写代码的时候，可以不用对最后一天单独处理，输出最后一天，状态为 0 的时候的值即可。
**第 3 步：确定初始值**
起始的时候：

如果什么都不做，dp\[0][0] = 0；
如果持有股票，当前拥有的现金数是当天股价的相反数，即 dp\[0][1] = -prices[i]；
第 4 步：确定输出值
终止的时候，上面也分析了，输出 dp\[len - 1][0]，因为一定有 dp\[len - 1][0] > dp\[len - 1][1]。

**复杂度分析：**

- 时间复杂度：O(N)，这里 NN 表示股价数组的长度；
- 空间复杂度：O(N)，虽然是二维数组，但是第二维是常数，与问题规模无关。

###### 暴力搜索（超时）

```java
public class Solution {

    private int res;

    public int maxProfit(int[] prices) {
        int len = prices.length;
        if (len < 2) {
            return 0;
        }
        this.res = 0;
        dfs(prices, 0, len, 0, res);
        return this.res;
    }

    /**
     * @param prices 股价数组
     * @param index  当前是第几天，从 0 开始
     * @param status 0 表示不持有股票，1表示持有股票，
     * @param profit 当前收益
     */
    private void dfs(int[] prices, int index, int len, int status, int profit) {

        if (index == len) {
            this.res = Math.max(this.res, profit);
            return;
        }

        dfs(prices, index + 1, len, status, profit);

        if (status == 0) {
            // 可以尝试转向 1
            dfs(prices, index + 1, len, 1, profit - prices[index]);

        } else {
            // 此时 status == 1，可以尝试转向 0
            dfs(prices, index + 1, len, 0, profit + prices[index]);
        }
    }
}
```

根据题意：由于不限制交易次数，在每一天，就可以根据当前是否持有股票选择相应的操作。「暴力搜索」在树形问题里也叫「回溯搜索」、「回溯法」。

首先画出树形图，然后编码。java

![image.png](https://pic.leetcode-cn.com/3ede46d543e0fcd2ec1b4f9031637fd404b4bdf73505bb1233d870ac92ea8283-image.png)



