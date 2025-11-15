# 题目

给定一个二叉树（具有根结点 `root`）， 一个目标结点 `target` ，和一个整数值 `K` 。

返回到目标结点 `target` 距离为 `K` 的所有结点的值的列表。 答案可以以任何顺序返回。

**示例 1：**

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/06/28/sketch0.png)

```
输入：root = [3,5,1,6,2,0,8,null,null,7,4], target = 5, K = 2
输出：[7,4,1]
解释：
所求结点为与目标结点（值为 5）距离为 2 的结点，
值分别为 7，4，以及 1
注意，输入的 "root" 和 "target" 实际上是树上的结点。
上面的输入仅仅是对这些对象进行了序列化描述。
```

**提示：**

1. 给定的树是非空的。
2. 树上的每个结点都具有唯一的值 `0 <= node.val <= 500` 。
3. 目标结点 `target` 是树上的结点。
4. `0 <= K <= 1000`.

## 我的解法

未解出， 一直在想再二叉树中如何解决，没有想到将二叉树转换成图进行解题！

## 其他解法

### 深度优先搜索 + 哈希表

```c++
class Solution {
    unordered_map<int, TreeNode*> parents;
    vector<int> ans;

    void findParents(TreeNode* node) {
        if (node->left != nullptr) {
            parents[node->left->val] = node;
            findParents(node->left);
        }
        if (node->right != nullptr) {
            parents[node->right->val] = node;
            findParents(node->right);
        }
    }

    void findAns(TreeNode* node, TreeNode* from, int depth, int k) {
        if (node == nullptr) {
            return;
        }
        if (depth == k) {
            ans.push_back(node->val);
            return;
        }
        if (node->left != from) {
            findAns(node->left, node, depth + 1, k);
        }
        if (node->right != from) {
            findAns(node->right, node, depth + 1, k);
        }
        if (parents[node->val] != from) {
            findAns(parents[node->val], node, depth + 1, k);
        }
    }

public:
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        // 从 root 出发 DFS，记录每个结点的父结点
        findParents(root);

        // 从 target 出发 DFS，寻找所有深度为 k 的结点
        findAns(target, nullptr, 0, k);

        return ans;
    }
};
```

```java
class Solution {
    Map<Integer, TreeNode> parents = new HashMap<Integer, TreeNode>();
    List<Integer> ans = new ArrayList<Integer>();

    public List<Integer> distanceK(TreeNode root, TreeNode target, int k) {
        // 从 root 出发 DFS，记录每个结点的父结点
        findParents(root);

        // 从 target 出发 DFS，寻找所有深度为 k 的结点
        findAns(target, null, 0, k);

        return ans;
    }

    public void findParents(TreeNode node) {
        if (node.left != null) {
            parents.put(node.left.val, node);
            findParents(node.left);
        }
        if (node.right != null) {
            parents.put(node.right.val, node);
            findParents(node.right);
        }
    }

    public void findAns(TreeNode node, TreeNode from, int depth, int k) {
        if (node == null) {
            return;
        }
        if (depth == k) {
            ans.add(node.val);
            return;
        }
        if (node.left != from) {
            findAns(node.left, node, depth + 1, k);
        }
        if (node.right != from) {
            findAns(node.right, node, depth + 1, k);
        }
        if (parents.get(node.val) != from) {
            findAns(parents.get(node.val), node, depth + 1, k);
        }
    }
}
```

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def distanceK(self, root: TreeNode, target: TreeNode, k: int) -> List[int]:
        parents = {}
        ans = []

        def findParents(node):
            if node.left != None:
                parents[node.left] = node
                findParents(node.left)
            if node.right != None:
                parents[node.right] = node
                findParents(node.right)
            
        def findAns(node, prev, depth):
            if node == None:
                return
            if depth == k:
                ans.append(node.val)
                return
            if node.left != prev:
                findAns(node.left, node, depth + 1)
            if node.right != prev:
                findAns(node.right, node, depth + 1)
            if node in parents and parents[node] != prev:
                findAns(parents[node], node, depth + 1)
        
        findParents(root)
        findAns(target, None, 0)
        return ans
```

若将 $\textit{target}$​ 当作树的根结点，我们就能从 $\textit{target}$​​ 出发，使用深度优先搜索去寻找与 $\textit{target}$​​ 距离为 k 的所有结点，即深度为 k 的所有结点。

由于输入的二叉树没有记录父结点，为此，我们从根结点 $\textit{root}$ 出发，使用深度优先搜索遍历整棵树，同时用一个哈希表记录每个结点的父结点。

然后从 $\textit{target}$ 出发，使用深度优先搜索遍历整棵树，除了搜索左右儿子外，还可以顺着父结点向上搜索。

代码实现时，由于每个结点值都是唯一的，哈希表的键可以用结点值代替。此外，为避免在深度优先搜索时重复访问结点，递归时额外传入来源结点 $\textit{from}$，在递归前比较目标结点是否与来源结点相同，不同的情况下才进行递归。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是二叉树的结点个数。需要执行两次深度优先搜索，每次的时间复杂度均为 O(n)。

- 空间复杂度：O(n)。记录父节点需要 O(n) 的空间，深度优先搜索需要 O(n) 的栈空间。


