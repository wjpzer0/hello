# 题目

**最大树** 定义：一棵树，并满足：其中每个节点的值都大于其子树中的任何其他值。

给你最大树的根节点 `root` 和一个整数 `val` 。

就像 TOP_654. 最大二叉树 例程从列表 `a`（`root = Construct(a)`）递归地构建的：

- 如果 `a` 为空，返回 `null` 。
- 否则，令 `a[i]` 作为 `a` 的最大元素。创建一个值为 `a[i]` 的根节点 `root` 。
- `root` 的左子树将被构建为 `Construct([a[0], a[1], ..., a[i - 1]])` 。
- `root` 的右子树将被构建为 `Construct([a[i + 1], a[i + 2], ..., a[a.length - 1]])` 。
- 返回 `root` 。

请注意，题目没有直接给出 `a` ，只是给出一个根节点 `root = Construct(a)` 。

假设 `b` 是 `a` 的副本，并在末尾附加值 `val`。题目数据保证 `b` 中的值互不相同。

返回 `Construct(b)` 。

 

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-1-1.png)![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-1-2.png)**

```
输入：root = [4,1,3,null,null,2], val = 5
输出：[5,4,null,1,3,null,null,2]
解释：a = [1,4,2,3], b = [1,4,2,3,5]
```

**示例 2：
![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-2-1.png)![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-2-2.png)**

```
输入：root = [5,2,4,null,1], val = 3
输出：[5,2,4,null,1,null,3]
解释：a = [2,1,5,4], b = [2,1,5,4,3]
```

**示例 3：
![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-3-1.png)![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/maximum-binary-tree-3-2.png)**

```
输入：root = [5,2,3,null,1], val = 4
输出：[5,2,4,null,1,3]
解释：a = [2,1,5,3], b = [2,1,5,3,4]
```

**提示：**

- 树中节点数目在范围 `[1, 100]` 内
- `1 <= Node.val <= 100`
- 树中的所有值 **互不相同**
- `1 <= val <= 100`

## 我的解法

找到A后重新构造！十分憨批，没有运用到树的特性！

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def insertIntoMaxTree(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        def findA(node):
            if node == None:
                return []
            leftList = findA(node.left)
            rightList = findA(node.right)
            return leftList + [node.val] + rightList
        a = findA(root)
        a.append(val)

        def constructTree(A):
            n = len(A)
            stk = list()
            tree = [None] * n
            for i in range(n):
                tree[i] = TreeNode(A[i])
                while stk and A[i] > A[stk[-1]]:
                    tree[i].left = tree[stk[-1]]
                    stk.pop()
                if stk:
                    tree[stk[-1]].right = tree[i]
                stk.append(i)
            return tree[stk[0]]
            
        return constructTree(a)
```

## 其它解法

### 遍历右子节点

```c++
class Solution {
public:
    TreeNode* insertIntoMaxTree(TreeNode* root, int val) {
        TreeNode* parent = nullptr;
        TreeNode* cur = root;
        while (cur) {
            if (val > cur->val) {
                if (!parent) {
                    return new TreeNode(val, root, nullptr);
                }
                TreeNode* node = new TreeNode(val, cur, nullptr);
                parent->right = node;
                return root;
            }
            else {
                parent = cur;
                cur = cur->right;
            }
        }
        parent->right = new TreeNode(val);
        return root;
    }
};
```

```java
class Solution {
    public TreeNode insertIntoMaxTree(TreeNode root, int val) {
        TreeNode parent = null;
        TreeNode cur = root;
        while (cur != null) {
            if (val > cur.val) {
                if (parent == null) {
                    return new TreeNode(val, root, null);
                }
                TreeNode node = new TreeNode(val, cur, null);
                parent.right = node;
                return root;
            } else {
                parent = cur;
                cur = cur.right;
            }
        }
        parent.right = new TreeNode(val);
        return root;
    }
}
```

```python
class Solution:
    def insertIntoMaxTree(self, root: Optional[TreeNode], val: int) -> Optional[TreeNode]:
        parent, cur = None, root
        while cur:
            if val > cur.val:
                if not parent:
                    return TreeNode(val, root, None)
                node = TreeNode(val, cur, None)
                parent.right = node
                return root
            else:
                parent = cur
                cur = cur.right
        
        parent.right = TreeNode(val)
        return root
```

#### 思路与算法

如果根节点的值小于给定的整数 $\textit{val}$，那么新的树会以$ \textit{val}$作为根节点，并将原来的树作为新的根节点的左子树。

否则，我们从根节点开始不断地向右子节点进行遍历。这是因为，当遍历到的节点的值大于 $\textit{val}$时，由于$ \textit{val}$是新添加的位于数组末尾的元素，那么在构造的结果中，$\textit{val}$一定出现在该节点的右子树中。

当我们遍历到节点 $\textit{cur}$以及它的父节点 $\textit{parent}$，并且 $\textit{cur}$节点的值小于 $\textit{val}$时，我们就可以停止遍历，构造一个新的节点，以 $\textit{val}$为值且以 $\textit{cur}$为左子树。我们将该节点作为 $\textit{parent}$的新的右节点，并返回根节点作为答案即可。

如果遍历完成之后，仍然没有找到比 $\textit{val}$值小的节点，那么我们构造一个新的节点，以 $\textit{val}$为值，将该节点作为 $\textit{parent}$的右节点，并返回根节点作为答案即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是给定的树中的节点个数。在最坏情况下，树呈现链状结构，前 n-1 个节点有唯一的右子节点，并且 $\textit{val}$比树中任一节点的值都要小，此时需要遍历完整棵树，时间复杂度为 O(n)。

- 空间复杂度：O(1)。


