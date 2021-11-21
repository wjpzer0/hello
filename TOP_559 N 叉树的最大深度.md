# 题目

给定一个 N 叉树，找到其最大深度。

最大深度是指从根节点到最远叶子节点的最长路径上的节点总数。

N 叉树输入按层序遍历序列化表示，每组子节点由空值分隔（请参见示例）。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)

```
输入：root = [1,null,3,2,4,null,5,6]
输出：3
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2019/11/08/sample_4_964.png)

```
输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
输出：5
```

 

**提示：**

- 树的深度不会超过 `1000` 。
- 树的节点数目位于 $[0, 10^4] $之间。

## 我的解法

代码可以优化！除了深度优先搜索，还需要复习广度优先搜索！

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def maxDepth(self, root: 'Node') -> int:

        def dfs(node):
            if node == None:
                return 0
            child = node.children
            maxd = 0
            for ch in child:
                maxd = max(maxd, dfs(ch))
            return maxd + 1
        
        return dfs(root)
```

## 其他解法

### 深度优先搜索

```c++
class Solution {
public:
    int maxDepth(Node* root) {
        if (root == nullptr) {
            return 0;
        }
        int maxChildDepth = 0;
        vector<Node *> children = root->children;
        for (auto child : children) {
            int childDepth = maxDepth(child);
            maxChildDepth = max(maxChildDepth, childDepth);
        }
        return maxChildDepth + 1;
    }
};
```

```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) {
            return 0;
        }
        int maxChildDepth = 0;
        List<Node> children = root.children;
        for (Node child : children) {
            int childDepth = maxDepth(child);
            maxChildDepth = Math.max(maxChildDepth, childDepth);
        }
        return maxChildDepth + 1;
    }
}
```

```python
class Solution:
    def maxDepth(self, root: 'Node') -> int:
        return max((self.maxDepth(child) for child in root.children), default=0) + 1 if root else 0
```

如果根节点有 N 个子节点，则这 N 个子节点对应 N 个子树。记 N 个子树的最大深度中的最大值为 $\textit{maxChildDepth}$，则该 N 叉树的最大深度为 $\textit{maxChildDepth} + 1$。

每个子树的最大深度又可以以同样的方式进行计算。因此我们可以用「深度优先搜索」的方法计算 N 叉树的最大深度。具体而言，在计算当前 N 叉树的最大深度时，可以先递归计算出其每个子树的最大深度，然后在 O(1) 的时间内计算出当前 N 叉树的最大深度。递归在访问到空节点时退出。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为 N 叉树节点的个数。每个节点在递归中只被遍历一次。

- 空间复杂度：$O(\textit{height})$，其中$ \textit{height}$ 表示 N 叉树的高度。递归函数需要栈空间，而栈空间取决于递归的深度，因此空间复杂度等价于 N 叉树的高度。


### 广度优先搜索

```c++
class Solution {
public:
    int maxDepth(Node* root) {
        if (root == nullptr) {
            return 0;
        }
        queue<Node *> qu;
        qu.push(root);
        int ans = 0;
        while (!qu.empty()) {
            int size = qu.size();
            while (size > 0) {
                Node * node = qu.front();
                qu.pop();
                vector<Node *> children = node->children;
                for (auto child : children) {
                    qu.push(child);
                }
                size--;
            }
            ans++;
        }
        return ans;
    }
};
```

```java
class Solution {
    public int maxDepth(Node root) {
        if (root == null) {
            return 0;
        }
        Queue<Node> queue = new LinkedList<Node>();
        queue.offer(root);
        int ans = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            while (size > 0) {
                Node node = queue.poll();
                List<Node> children = node.children;
                for (Node child : children) {
                    queue.offer(child);
                }
                size--;
            }
            ans++;
        }
        return ans;
    }
}
```

```python
class Solution:
    def maxDepth(self, root: 'Node') -> int:
        if root is None:
            return 0
        ans = 0
        queue = [root]
        while queue:
            queue = [child for node in queue for child in node.children]
            ans += 1
        return ans
```

我们也可以用「广度优先搜索」的方法来解决这道题目，但我们需要对其进行一些修改，此时我们广度优先搜索的队列里存放的是「当前层的所有节点」。每次拓展下一层的时候，不同于广度优先搜索的每次只从队列里拿出一个节点，我们需要将队列里的所有节点都拿出来进行拓展，这样能保证每次拓展完的时候队列里存放的是当前层的所有节点，即我们是一层一层地进行拓展。最后我们用一个变量 $\textit{ans}$ 来维护拓展的次数，该 N 叉树的最大深度即为 $\textit{ans}$。

#### 复杂度分析

时间复杂度：O(n)，其中 n 为 N 叉树的节点个数。与方法一同样的分析，每个节点只会被访问一次。

空间复杂度：此方法空间的消耗取决于队列存储的元素数量，其在最坏情况下会达到 O(n)。

