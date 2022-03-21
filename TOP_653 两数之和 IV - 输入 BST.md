# 题目

给定一个二叉搜索树 `root` 和一个目标结果 `k`，如果 BST 中存在两个元素且它们的和等于给定的目标结果，则返回 `true`。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/21/sum_tree_1.jpg)

```
输入: root = [5,3,6,2,4,null,7], k = 9
输出: true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2020/09/21/sum_tree_2.jpg)

```
输入: root = [5,3,6,2,4,null,7], k = 28
输出: false
```

**提示:**

- 二叉树的节点个数的范围是 $[1, 10^4]$.
- $-10^4 <= Node.val <= 10^4$
- `root` 为二叉搜索树
- $-10^5 <= k <= 10^5$

## 我的解法

思路没毛病！

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def findTarget(self, root: Optional[TreeNode], k: int) -> bool:
        vis = set()
        dq = deque()
        dq.append(root)
        while(dq):
            node = dq.popleft()
            if not node:
                continue
            if node.val in vis:
                return True
            dq.append(node.right)
            dq.append(node.left)
            vis.add(k - node.val)
        return False
```

## 其他解法

### 深度优先搜索 + 哈希表

```c++
class Solution {
public:
    unordered_set<int> hashTable;

    bool findTarget(TreeNode *root, int k) {
        if (root == nullptr) {
            return false;
        }
        if (hashTable.count(k - root->val)) {
            return true;
        }
        hashTable.insert(root->val);
        return findTarget(root->left, k) || findTarget(root->right, k);
    }
};
```

```java
class Solution {
    Set<Integer> set = new HashSet<Integer>();

    public boolean findTarget(TreeNode root, int k) {
        if (root == null) {
            return false;
        }
        if (set.contains(k - root.val)) {
            return true;
        }
        set.add(root.val);
        return findTarget(root.left, k) || findTarget(root.right, k);
    }
}
```

```python
class Solution:
    def __init__(self):
        self.s = set()

    def findTarget(self, root: Optional[TreeNode], k: int) -> bool:
        if root is None:
            return False
        if k - root.val in self.s:
            return True
        self.s.add(root.val)
        return self.findTarget(root.left, k) or self.findTarget(root.right, k)
```

#### 思路和算法

我们可以使用深度优先搜索的方式遍历整棵树，用哈希表记录遍历过的节点的值。

对于一个值为 x 的节点，我们检查哈希表中是否存在 k - x 即可。如果存在对应的元素，那么我们就可以在该树上找到两个节点的和为 k；否则，我们将 x 放入到哈希表中。

如果遍历完整棵树都不存在对应的元素，那么该树上不存在两个和为 kk 的节点。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为二叉搜索树的大小。我们需要遍历整棵树一次。

- 空间复杂度：O(n)，其中 n 为二叉搜索树的大小。主要为哈希表的开销，最坏情况下我们需要将每个节点加入哈希表一次。


### 广度优先搜索 + 哈希表

```c++
class Solution {
public:
    bool findTarget(TreeNode *root, int k) {
        unordered_set<int> hashTable;
        queue<TreeNode *> que;
        que.push(root);
        while (!que.empty()) {
            TreeNode *node = que.front();
            que.pop();
            if (hashTable.count(k - node->val)) {
                return true;
            }
            hashTable.insert(node->val);
            if (node->left != nullptr) {
                que.push(node->left);
            }
            if (node->right != nullptr) {
                que.push(node->right);
            }
        }
        return false;
    }
};
```

```java
class Solution {
    public boolean findTarget(TreeNode root, int k) {
        Set<Integer> set = new HashSet<Integer>();
        Queue<TreeNode> queue = new ArrayDeque<TreeNode>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            if (set.contains(k - node.val)) {
                return true;
            }
            set.add(node.val);
            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }
        return false;
    }
}
```

```python
class Solution:
    def findTarget(self, root: Optional[TreeNode], k: int) -> bool:
        s = set()
        q = deque([root])
        while q:
            node = q.popleft()
            if k - node.val in s:
                return True
            s.add(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        return False
```

#### 思路和算法

我们可以使用广度优先搜索的方式遍历整棵树，用哈希表记录遍历过的节点的值。

具体地，我们首先创建一个哈希表和一个队列，将根节点加入队列中，然后执行以下步骤：

- 从队列中取出队头，假设其值为 x；
- 检查哈希表中是否存在 k - x，如果存在，返回 $\text{True}$；
- 否则，将该节点的左右的非空子节点加入队尾；
- 重复以上步骤，直到队列为空；
- 如果队列为空，说明树上不存在两个和为 kk 的节点，返回$ \text{False}$。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为二叉搜索树的大小。我们需要遍历整棵树一次。

- 空间复杂度：O(n)，其中 n 为二叉搜索树的大小。主要为哈希表和队列的开销，最坏情况下我们需要将每个节点加入哈希表和队列各一次。


### 深度优先搜索 + 中序遍历 + 双指针

```c++
class Solution {
public:
    vector<int> vec;

    void inorderTraversal(TreeNode *node) {
        if (node == nullptr) {
            return;
        }
        inorderTraversal(node->left);
        vec.push_back(node->val);
        inorderTraversal(node->right);
    }

    bool findTarget(TreeNode *root, int k) {
        inorderTraversal(root);
        int left = 0, right = vec.size() - 1;
        while (left < right) {
            if (vec[left] + vec[right] == k) {
                return true;
            }
            if (vec[left] + vec[right] < k) {
                left++;
            } else {
                right--;
            }
        }
        return false;
    }
};
```

```java
class Solution {
    List<Integer> list = new ArrayList<Integer>();

    public boolean findTarget(TreeNode root, int k) {
        inorderTraversal(root);
        int left = 0, right = list.size() - 1;
        while (left < right) {
            if (list.get(left) + list.get(right) == k) {
                return true;
            }
            if (list.get(left) + list.get(right) < k) {
                left++;
            } else {
                right--;
            }
        }
        return false;
    }

    public void inorderTraversal(TreeNode node) {
        if (node == null) {
            return;
        }
        inorderTraversal(node.left);
        list.add(node.val);
        inorderTraversal(node.right);
    }
}
```

```python
class Solution:
    def findTarget(self, root: Optional[TreeNode], k: int) -> bool:
        arr = []
        def inorderTraversal(node: Optional[TreeNode]) -> None:
            if node:
                inorderTraversal(node.left)
                arr.append(node.val)
                inorderTraversal(node.right)
        inorderTraversal(root)

        left, right = 0, len(arr) - 1
        while left < right:
            sum = arr[left] + arr[right]
            if sum == k:
                return True
            if sum < k:
                left += 1
            else:
                right -= 1
        return False
```

#### 思路和算法

注意到二叉搜索树的中序遍历是升序排列的，我们可以将该二叉搜索树的中序遍历的结果记录下来，得到一个升序数组。

这样该问题即转化为 TOP_167. 两数之和 II - 输入有序数组 。我们可以使用双指针解决它。

具体地，我们使用两个指针分别指向数组的头尾，当两个指针指向的元素之和小于 k 时，让左指针右移；当两个指针指向的元素之和大于 k 时，让右指针左移；当两个指针指向的元素之和等于 k 时，返回 $\text{True}$。

最终，当左指针和右指针重合时，树上不存在两个和为 k 的节点，返回 $\text{False}$。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为二叉搜索树的大小。我们需要遍历整棵树一次，并对得到的升序数组使用双指针遍历。

- 空间复杂度：O(n)，其中 n 为二叉搜索树的大小。主要为升序数组的开销。


### 迭代 + 中序遍历 + 双指针

```c++
class Solution {
public:
    TreeNode *getLeft(stack<TreeNode *> &stk) {
        TreeNode *root = stk.top();
        stk.pop();
        TreeNode *node = root->right;
        while (node != nullptr) {
            stk.push(node);
            node = node->left;
        }
        return root;
    }

    TreeNode *getRight(stack<TreeNode *> &stk) {
        TreeNode *root = stk.top();
        stk.pop();
        TreeNode *node = root->left;
        while (node != nullptr) {
            stk.push(node);
            node = node->right;
        }
        return root;
    }

    bool findTarget(TreeNode *root, int k) {
        TreeNode *left = root, *right = root;
        stack<TreeNode *> leftStack, rightStack;
        leftStack.push(left);
        while (left->left != nullptr) {
            leftStack.push(left->left);
            left = left->left;
        }
        rightStack.push(right);
        while (right->right != nullptr) {
            rightStack.push(right->right);
            right = right->right;
        }
        while (left != right) {
            if (left->val + right->val == k) {
                return true;
            }
            if (left->val + right->val < k) {
                left = getLeft(leftStack);
            } else {
                right = getRight(rightStack);
            }
        }
        return false;
    }
};
```

```java
class Solution {
    public boolean findTarget(TreeNode root, int k) {
        TreeNode left = root, right = root;
        Deque<TreeNode> leftStack = new ArrayDeque<TreeNode>();
        Deque<TreeNode> rightStack = new ArrayDeque<TreeNode>();
        leftStack.push(left);
        while (left.left != null) {
            leftStack.push(left.left);
            left = left.left;
        }
        rightStack.push(right);
        while (right.right != null) {
            rightStack.push(right.right);
            right = right.right;
        }
        while (left != right) {
            if (left.val + right.val == k) {
                return true;
            }
            if (left.val + right.val < k) {
                left = getLeft(leftStack);
            } else {
                right = getRight(rightStack);
            }
        }
        return false;
    }

    public TreeNode getLeft(Deque<TreeNode> stack) {
        TreeNode root = stack.pop();
        TreeNode node = root.right;
        while (node != null) {
            stack.push(node);
            node = node.left;
        }
        return root;
    }

    public TreeNode getRight(Deque<TreeNode> stack) {
        TreeNode root = stack.pop();
        TreeNode node = root.left;
        while (node != null) {
            stack.push(node);
            node = node.right;
        }
        return root;
    }
}
```

```python
class Solution:
    def findTarget(self, root: Optional[TreeNode], k: int) -> bool:
        left, right = root, root
        leftStk, rightStk = [left], [right]
        while left.left:
            left = left.left
            leftStk.append(left)
        while right.right:
            right = right.right
            rightStk.append(right)
        while left != right:
            sum = left.val + right.val
            if sum == k:
                return True
            if sum < k:
                left = leftStk.pop()
                node = left.right
                while node:
                    leftStk.append(node)
                    node = node.left
            else:
                right = rightStk.pop()
                node = right.left
                while node:
                    rightStk.append(node)
                    node = node.right
        return False
```

#### 思路和算法

在方法三中，我们是在中序遍历得到的数组上进行双指针，这样需要消耗 O(n) 的空间，实际上我们可以将双指针的移动理解为在树上的遍历过程的一次移动。因为递归方法较难控制移动过程，因此我们使用迭代的方式进行遍历。

具体地，我们对于每个指针新建一个栈。初始，我们让左指针移动到树的最左端点，并将路径保存在栈中，接下来我们可以依据栈来 O(1) 地计算出左指针的下一个位置。右指针也是同理。

计算下一个位置时，我们首先将位于栈顶的当前节点从栈中弹出，此时首先判断当前节点是否存在右子节点，如果存在，那么我们将右子节点的最左子树加入到栈中；否则我们就完成了当前层的遍历，无需进一步修改栈的内容，直接回溯到上一层即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为二叉搜索树的大小。在双指针的过程中，我们实际上遍历了整棵树一次。

- 空间复杂度：O(n)，其中 n 为二叉搜索树的大小。主要为栈的开销，最坏情况下二叉搜索树为一条链，需要 O(n) 的栈空间。


