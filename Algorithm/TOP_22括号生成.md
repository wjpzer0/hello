#### 题目

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

示例：

```
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]

```

#### 我的解法

没有任何思路ε(┬┬﹏┬┬)3

#### 其他解法

###### 深度优先遍历

```python
#做减法
from typing import List
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:

        res = []
        cur_str = ''

        def dfs(cur_str, left, right):
            """
            :param cur_str: 从根结点到叶子结点的路径字符串
            :param left: 左括号还可以使用的个数
            :param right: 右括号还可以使用的个数
            :return:
            """
            if left == 0 and right == 0:
                res.append(cur_str)
                return
            if right < left:
                return
            if left > 0:
                dfs(cur_str + '(', left - 1, right)
            if right > 0:
                dfs(cur_str + ')', left, right - 1)

        dfs(cur_str, n, n)
        return res
```

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    // 做加法
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        // 特判
        if (n == 0) {
            return res;
        }
        dfs("", 0, 0, n, res);
        return res;
    }
    /**
     * @param curStr 当前递归得到的结果
     * @param left   左括号已经用了几个
     * @param right  右括号已经用了几个
     * @param n      左括号、右括号一共得用几个
     * @param res    结果集
     */
    private void dfs(String curStr, int left, int right, int n, List<String> res) {
        if (left == n && right == n) {
            res.add(curStr);
            return;
        }
        // 剪枝
        if (left < right) {
            return;
        }
        if (left < n) {
            dfs(curStr + "(", left + 1, right, n, res);
        }
        if (right < n) {
            dfs(curStr + ")", left, right + 1, n, res);
        }
    }
}
```



我们以 n = 2 为例，画树形结构图。方法是 「做减法」。

![LeetCode 第 22 题：“括号生出”题解配图.png](https://pic.leetcode-cn.com/7ec04f84e936e95782aba26c4663c5fe7aaf94a2a80986a97d81574467b0c513-LeetCode%20%E7%AC%AC%2022%20%E9%A2%98%EF%BC%9A%E2%80%9C%E6%8B%AC%E5%8F%B7%E7%94%9F%E5%87%BA%E2%80%9D%E9%A2%98%E8%A7%A3%E9%85%8D%E5%9B%BE.png)

画图以后，可以分析出的结论：

当前左右括号都有大于 0 个可以使用的时候，才产生分支；
产生左分支的时候，只看当前是否还有左括号可以使用；
产生右分支的时候，还受到左分支的限制，右边剩余可以使用的括号数量一定得在严格大于左边剩余的数量的时候，才可以产生分支；
在左边和右边剩余的括号数都等于 0 的时候结算。

如果我们不用减法，使用加法，即 `left` 表示「左括号还有几个没有用掉」，`right` 表示「右括号还有几个没有用掉」，可以画出另一棵递归树。

![image.png](https://pic.leetcode-cn.com/efbe574e5e6addcd1c9dc5c13a50c6f162a2b14a95d6aed2c394e18287a067fa-image.png)

###### 广度优先遍历

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class Solution {

    class Node {
        /**
         * 当前得到的字符串
         */
        private String res;
        /**
         * 剩余左括号数量
         */
        private int left;
        /**
         * 剩余右括号数量
         */
        private int right;

        public Node(String res, int left, int right) {
            this.res = res;
            this.left = left;
            this.right = right;
        }

        @Override
        public String toString() {
            return "Node{" +
                    "res='" + res + '\'' +
                    ", left=" + left +
                    ", right=" + right +
                    '}';
        }
    }

    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if (n == 0) {
            return res;
        }
        Queue<Node> queue = new LinkedList<>();
        queue.offer(new Node("", n, n));
        // 总共需要拼凑的字符总数是 2 * n
        n = 2 * n;
        while (n > 0) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Node curNode = queue.poll();
                if (curNode.left > 0) {
                    queue.offer(new Node(curNode.res + "(", curNode.left - 1, curNode.right));
                }
                if (curNode.right > 0 && curNode.left < curNode.right) {
                    queue.offer(new Node(curNode.res + ")", curNode.left, curNode.right - 1));
                }
            }
            n--;
        }

        // 最后一层就是题目要求的结果集
        while (!queue.isEmpty()) {
            res.add(queue.poll().res);
        }
        return res;
    }
} 
```

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Deque;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.Stack;

public class Solution {
    class Node {
        /**
         * 当前得到的字符串
         */
        private String res;
        /**
         * 剩余左括号数量
         */
        private int left;
        /**
         * 剩余右括号数量
         */
        private int right;

        public Node(String str, int left, int right) {
            this.res = str;
            this.left = left;
            this.right = right;
        }
    }
    
    // 注意：这是深度优先遍历
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if (n == 0) {
            return res;
        }

        // 查看了 Stack 源码，官方推荐使用 Deque 对象，
        // 注意：只使用栈相关的接口，即只使用 `addLast()` 和 `removeLast()`
        Deque<Node> stack = new ArrayDeque<>();
        stack.addLast(new Node("", n, n));

        while (!stack.isEmpty()) {

            Node curNode = stack.removeLast();
            if (curNode.left == 0 && curNode.right == 0) {
                res.add(curNode.res);
            }
            if (curNode.left > 0) {
                stack.addLast(new Node(curNode.res + "(", curNode.left - 1, curNode.right));
            }
            if (curNode.right > 0 && curNode.left < curNode.right) {
                stack.addLast(new Node(curNode.res + ")", curNode.left, curNode.right - 1));
            }
        }
        return res;
    }
}
// Java 代码仅仅是把 Queue 换成了 Stack ，广度优先遍历就改成了深度优先遍历。
```

通过编写广度优先遍历的代码，读者可以体会一下，为什么搜索几乎都是用深度优先遍历（回溯算法）。

广度优先遍历，得程序员自己编写结点类，显示使用队列这个数据结构。深度优先遍历的时候，就可以直接使用系统栈，在递归方法执行完成的时候，系统栈顶就把我们所需要的状态信息直接弹出，而无须编写结点类和显示使用栈。

下面的代码，读者可以把 Queue 换成 Stack，提交以后，也可以得到 Accept。

读者可以通过比较：

- 广度优先遍历；
- 自己使用栈编写深度优先遍历；
- 使用系统栈的深度优先遍历（回溯算法）。

来理解「回溯算法」作为一种「搜索算法」的合理性。

还是上面的题解配图（1），使用广度优先遍历，结果集都在最后一层，即叶子结点处得到所有的结果集，编写代码如下。

###### 动态规划

```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        if n == 0:
            return []
        total_l = []
        total_l.append([None])    # 0组括号时记为None
        total_l.append(["()"])    # 1组括号只有一种情况
        for i in range(2,n+1):    # 开始计算i组括号时的括号组合
            l = []        
            for j in range(i):    # 开始遍历 p q ，其中p+q=i-1 , j 作为索引
                now_list1 = total_l[j]    # p = j 时的括号组合情况
                now_list2 = total_l[i-1-j]    # q = (i-1) - j 时的括号组合情况
                for k1 in now_list1:  
                    for k2 in now_list2:
                        if k1 == None:
                            k1 = ""
                        if k2 == None:
                            k2 = ""
                        el = "(" + k1 + ")" + k2
                        l.append(el)    # 把所有可能的情况添加到 l 中
            total_l.append(l)    # l这个list就是i组括号的所有情况，添加到total_l中，继续求解i=i+1的情况
        return total_l[n]
/**
* 动态规划
* 从f(1),f(2),...,f(n-1)构建出f(n)
*
* 分析一个解的构成：必然从左括号开始，而这个开始的左括号必然对应着一个一个右括号，
*     因此，任何一个解可以表示为 (A)B。
* 令f(n) = (A)B  其中A和B是规模更小时问题的合法解，并且A、B可以为空，
* 并且A中括号对数+B中括号对数之和为n-1
*
* 也就是说，可以从f(0),f(1),f(2),...,f(n-1)构建出f(n),其中f0 = ""表示空解。
*/
```

考虑 `i=n` 时相比 `n-1` 组括号增加的那一组括号的位置

**思路：**
当我们清楚所有 i<n 时括号的可能生成排列后，对与 i=n 的情况，我们考虑整个括号排列中最左边的括号。
它一定是一个左括号，那么它可以和它对应的右括号组成一组完整的括号 "( )"，我们认为这一组是相比 n-1 增加进来的括号。

那么，剩下 n-1 组括号有可能在哪呢？

**【这里是重点，请着重理解】**

剩下的括号要么在这一组新增的括号内部，要么在这一组新增括号的外部（右侧）。

既然知道了 i<n 的情况，那我们就可以对所有情况进行遍历：

"(" + 【i=p时所有括号的排列组合】 + ")" + 【i=q时所有括号的排列组合】

其中 p + q = n-1，且 p q 均为非负整数。

事实上，当上述 p 从 0 取到 n-1，q 从 n-1 取到 0 后，所有情况就遍历完了。

注：上述遍历是没有重复情况出现的，即当 (p1,q1)≠(p2,q2) 时，按上述方式取的括号组合一定不同。

