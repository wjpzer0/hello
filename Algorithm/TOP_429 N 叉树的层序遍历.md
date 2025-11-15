# 题目

给定一个 N 叉树，返回其节点值的*层序遍历*。（即从左到右，逐层遍历）。

树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)

```
输入：root = [1,null,3,2,4,null,5,6]
输出：[[1],[3,2,4],[5,6]]
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2019/11/08/sample_4_964.png)

```
输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
输出：[[1],[2,3,4,5],[6,7,8,9,10],[11,12,13],[14]]
```

 

**提示：**

- 树的高度不会超过 `1000`
- 树的节点总数在$ [0, 10^4]$ 之间

## 我的解法

dfs，嗯还是不熟悉！

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children
"""

class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        ans = []
        if root == None:
            return ans
        mid = deque()
        mid.append(root)
        newMid = deque()
        midList = []
        ans.append([root.val])
        while mid:
            midNode = mid.popleft()
            for node in midNode.children:
                newMid.append(node)
                midList.append(node.val)
            if len(mid) == 0:
                mid = newMid
                ans.append(midList)
                midList = []
                newMid = deque()
        return ans[:-1]
```

## 其他解法

### 广度优先搜索

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        if (!root) {
            return {};
        }

        vector<vector<int>> ans;
        queue<Node*> q;
        q.push(root);

        while (!q.empty()) {
            int cnt = q.size();
            vector<int> level;
            for (int i = 0; i < cnt; ++i) {
                Node* cur = q.front();
                q.pop();
                level.push_back(cur->val);
                for (Node* child: cur->children) {
                    q.push(child);
                }
            }
            ans.push_back(move(level));
        }

        return ans;
    }
};
```

```java
class Solution {
    public List<List<Integer>> levelOrder(Node root) {
        if (root == null) {
            return new ArrayList<List<Integer>>();
        }

        List<List<Integer>> ans = new ArrayList<List<Integer>>();
        Queue<Node> queue = new ArrayDeque<Node>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int cnt = queue.size();
            List<Integer> level = new ArrayList<Integer>();
            for (int i = 0; i < cnt; ++i) {
                Node cur = queue.poll();
                level.add(cur.val);
                for (Node child : cur.children) {
                    queue.offer(child);
                }
            }
            ans.add(level);
        }

        return ans;
    }
}
```

```python
class Solution:
    def levelOrder(self, root: 'Node') -> List[List[int]]:
        if not root:
            return []

        ans = list()
        q = deque([root])

        while q:
            cnt = len(q)
            level = list()
            for _ in range(cnt):
                cur = q.popleft()
                level.append(cur.val)
                for child in cur.children:
                    q.append(child)
            ans.append(level)

        return ans
```

#### 思路与算法

对于「层序遍历」的题目，我们一般使用广度优先搜索。在广度优先搜索的每一轮中，我们会遍历同一层的所有节点。

具体地，我们首先把根节点 $\textit{root}$ 放入队列中，随后在广度优先搜索的每一轮中，我们首先记录下当前队列中包含的节点个数（记为 $\textit{cnt}$），即表示上一层的节点个数。在这之后，我们从队列中依次取出节点，直到取出了上一层的全部 $\textit{cnt}$ 个节点为止。当取出节点 $\textit{cur}$ 时，我们将 $\textit{cur}$ 的值放入一个临时列表，再将 $\textit{cur}$ 的所有子节点全部放入队列中。

当这一轮遍历完成后，临时列表中就存放了当前层所有节点的值。这样一来，当整个广度优先搜索完成后，我们就可以得到层序遍历的结果。

#### 细节

需要特殊判断树为空的情况。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是树中包含的节点个数。在广度优先搜索的过程中，我们需要遍历每一个节点恰好一次。

- 空间复杂度：O(n)，即为队列需要使用的空间。在最坏的情况下，树只有两层，且最后一层有 n - 1 个节点，此时就需要 O(n) 的空间。


