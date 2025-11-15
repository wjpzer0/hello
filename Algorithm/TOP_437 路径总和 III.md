# 	题目

给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)

```
输入：root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
输出：3
解释：和等于 8 的路径有 3 条，如图所示。
```

**示例 2：**

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：3
```

 

**提示:**

- 二叉树的节点个数的范围是 `[0,1000]`
- $-10^9 <= Node.val <= 10^9$
- `-1000 <= targetSum <= 1000` 

## 我的解法

没有想法，想划水！

## 其他解法

### 深度优先搜索

```c++
class Solution {
public:
    int rootSum(TreeNode* root, int targetSum) {
        if (!root) {
            return 0;
        }

        int ret = 0;
        if (root->val == targetSum) {
            ret++;
        } 

        ret += rootSum(root->left, targetSum - root->val);
        ret += rootSum(root->right, targetSum - root->val);
        return ret;
    }

    int pathSum(TreeNode* root, int targetSum) {
        if (!root) {
            return 0;
        }
        
        int ret = rootSum(root, targetSum);
        ret += pathSum(root->left, targetSum);
        ret += pathSum(root->right, targetSum);
        return ret;
    }
};
```

```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        if (root == null) {
            return 0;
        }

        int ret = rootSum(root, targetSum);
        ret += pathSum(root.left, targetSum);
        ret += pathSum(root.right, targetSum);
        return ret;
    }

    public int rootSum(TreeNode root, int targetSum) {
        int ret = 0;

        if (root == null) {
            return 0;
        }
        int val = root.val;
        if (val == targetSum) {
            ret++;
        } 

        ret += rootSum(root.left, targetSum - val);
        ret += rootSum(root.right, targetSum - val);
        return ret;
    }
}
```

```python
class Solution:
    def pathSum(self, root: TreeNode, targetSum: int) -> int:
        def rootSum(root, targetSum):
            if root is None:
                return 0

            ret = 0
            if root.val == targetSum:
                ret += 1

            ret += rootSum(root.left, targetSum - root.val)
            ret += rootSum(root.right, targetSum - root.val)
            return ret
        
        if root is None:
            return 0
            
        ret = rootSum(root, targetSum)
        ret += self.pathSum(root.left, targetSum)
        ret += self.pathSum(root.right, targetSum)
        return ret
```

#### 思路与算法

我们首先想到的解法是穷举所有的可能，我们访问每一个节点 $\textit{node}$，检测以 \textit{node}node 为起始节点且向下延深的路径有多少种。我们递归遍历每一个节点的所有可能的路径，然后将这些路径数目加起来即为返回结果。

- 我们首先定义 $\textit{rootSum}(p,\textit{val})$ 表示以节点 p 为起点向下且满足路径总和为 valval 的路径数目。我们对二叉树上每个节点 p 求出 $\textit{rootSum}(p,\textit{targetSum})$，然后对这些路径数目求和即为返回结果。

- 我们对节点 p 求$ \textit{rootSum}(p,\textit{targetSum})$ 时，以当前节点 p 为目标路径的起点递归向下进行搜索。假设当前的节点 p 的值为 $\textit{val}$，我们对左子树和右子树进行递归搜索，对节点 p 的左孩子节点 $p_{l}$求出 $\textit{rootSum}(p_{l},\textit{targetSum}-\textit{val})$，以及对右孩子节点 $p_{r}$求出 $\textit{rootSum}(p_{r},\textit{targetSum}-\textit{val})$。节点 p 的 $\textit{rootSum}(p,\textit{targetSum})$ 即等于 $\textit{rootSum}(p_{l},\textit{targetSum}-\textit{val})$ 与 $\textit{rootSum}(p_{r},\textit{targetSum}-\textit{val})$之和，同时我们还需要判断一下当前节点 p 的值是否刚好等于 $\textit{targetSum}$。
- 我们采用递归遍历二叉树的每个节点 p，对节点 p 求 $\textit{rootSum}(p,\textit{val})$，然后将每个节点所有求的值进行相加求和返回。


#### 复杂度分析

- 时间复杂度：$O(N^2)$，其中 N 为该二叉树节点的个数。对于每一个节点，求以该节点为起点的路径数目时，则需要遍历以该节点为根节点的子树的所有节点，因此求该路径所花费的最大时间为 O(N)，我们会对每个节点都求一次以该节点为起点的路径数目，因此时间复杂度为 $O(N^{2})$。
- 空间复杂度：O(N)，考虑到递归需要在栈上开辟空间。


### 前缀和

```c++
class Solution {
public:
    unordered_map<long long, int> prefix;

    int dfs(TreeNode *root, long long curr, int targetSum) {
        if (!root) {
            return 0;
        }

        int ret = 0;
        curr += root->val;
        if (prefix.count(curr - targetSum)) {
            ret = prefix[curr - targetSum];
        }

        prefix[curr]++;
        ret += dfs(root->left, curr, targetSum);
        ret += dfs(root->right, curr, targetSum);
        prefix[curr]--;

        return ret;
    }

    int pathSum(TreeNode* root, int targetSum) {
        prefix[0] = 1;
        return dfs(root, 0, targetSum);
    }
};
```

```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {
        HashMap<Long, Integer> prefix = new HashMap<>();
        prefix.put(0L, 1);
        return dfs(root, prefix, 0, targetSum);
    }

    public int dfs(TreeNode root, Map<Long, Integer> prefix, long curr, int targetSum) {
        if (root == null) {
            return 0;
        }

        int ret = 0;
        curr += root.val;

        ret = prefix.getOrDefault(curr - targetSum, 0);
        prefix.put(curr, prefix.getOrDefault(curr, 0) + 1);
        ret += dfs(root.left, prefix, curr, targetSum);
        ret += dfs(root.right, prefix, curr, targetSum);
        prefix.put(curr, prefix.getOrDefault(curr, 0) - 1);

        return ret;
    }
}
```

```python
class Solution:
    def pathSum(self, root: TreeNode, targetSum: int) -> int:
        prefix = collections.defaultdict(int)
        prefix[0] = 1

        def dfs(root, curr):
            if not root:
                return 0
            
            ret = 0
            curr += root.val
            ret += prefix[curr - targetSum]
            prefix[curr] += 1
            ret += dfs(root.left, curr)
            ret += dfs(root.right, curr)
            prefix[curr] -= 1

            return ret

        return dfs(root, 0)
```

#### 思路与算法

我们仔细思考一下，解法一中应该存在许多重复计算。我们定义节点的前缀和为：由根结点到当前结点的路径上所有节点的和。我们利用先序遍历二叉树，记录下根节点 $\textit{root}$ 到当前节点 p 的路径上除当前节点以外所有节点的前缀和，在已保存的路径前缀和中查找是否存在前缀和刚好等于当前节点到根节点的前缀和 curr 减去 $\textit{targetSum}$。

- 对于空路径我们也需要保存预先处理一下，此时因为空路径不经过任何节点，因此它的前缀和为 0。

- 假设根节点为$ \textit{root}$，我们当前刚好访问节点 $\textit{node}$，则此时从根节点 $\textit{root}$ 到节点 $\textit{node}$ 的路径（无重复节点）刚好为 $\textit{root} \rightarrow p_1 \rightarrow p_2 \rightarrow \ldots \rightarrow p_k \rightarrow \textit{node}$，此时我们可以已经保存了节点 $p_1, p_2, p_3, \ldots, p_k$的前缀和，并且计算出了节点 $\textit{node}$ 的前缀和。
- 假设当前从根节点 $\textit{root}$ 到节点 $\textit{node}$ 的前缀和为 $\textit{curr}$，则此时我们在已保存的前缀和查找是否存在前缀和刚好等于 $\textit{curr} - \textit{targetSum}$。假设从根节点 $\textit{root}$ 到节点 $\textit{node}$ 的路径中存在节点 $p_i$到根节点 $\textit{root}$ 的前缀和为 $\textit{curr} - \textit{targetSum}$，则节点 $p_{i+1}$到 $\textit{node}$ 的路径上所有节点的和一定为 $\textit{targetSum}$。
- 我们利用深度搜索遍历树，当我们退出当前节点时，我们需要及时更新已经保存的前缀和。


#### 复杂度分析

- 时间复杂度：O(N)，其中 N 为二叉树中节点的个数。利用前缀和只需遍历一次二叉树即可。
- 空间复杂度：O(N)。