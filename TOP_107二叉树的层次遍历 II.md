#### 题目

给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 [3,9,20,null,null,15,7],

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其自底向上的层次遍历为：

```
[
  [15,7],
  [9,20],
  [3]
]
```

#### 我的解法

未解出，较为迷茫

#### 其他解法

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def levelOrderBottom(self, root: TreeNode) -> List[List[int]]:
        queue = []                                                  # 结果列表
        cur = [root]                                                # 接下来要循环的当前层节点，存的是节点
        while cur:                                                  # 当前层存在结点时
            cur_layer_val = []                                      # 初始化当前层结果列表为空，存的是val
            next_layer_node = []                                    # 初始化下一层结点列表为空
            for node in cur:                                        # 遍历当前层的每一个结点
                if node:                                            # 如果该结点不为空，则进行记录
                    cur_layer_val.append(node.val)                  # 将该结点的值加入当前层结果列表的末尾
                    next_layer_node.extend([node.left, node.right]) # 将该结点的左右孩子结点加入到下一层结点列表
            if cur_layer_val:                                       # 只要当前层结果列表不为空
                queue.insert(0, cur_layer_val)                      # 则把当前层结果列表插入到队列首端
            cur = next_layer_node                                   # 下一层的结点变成当前层，接着循环
        return queue                                                # 返回结果队列
```

###### 广度优先搜索

```python
class Solution:
    def levelOrderBottom(self, root: TreeNode) -> List[List[int]]:
        levelOrder = list()
        if not root:
            return levelOrder
        
        q = collections.deque([root])
        while q:
            level = list()
            size = len(q)
            for _ in range(size):
                node = q.popleft()
                level.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
            levelOrder.append(level)

        return levelOrder[::-1]
```

树的层次遍历可以使用广度优先搜索实现。从根节点开始搜索，每次遍历同一层的全部节点，使用一个列表存储该层的节点值。

如果要求从上到下输出每一层的节点值，做法是很直观的，在遍历完一层节点之后，将存储该层节点值的列表添加到结果列表的尾部。这道题要求从下到上输出每一层的节点值，只要对上述操作稍作修改即可：在遍历完一层节点之后，将存储该层节点值的列表添加到结果列表的头部。

为了降低在结果列表的头部添加一层节点值的列表的时间复杂度，结果列表可以使用链表的结构，在链表头部添加一层节点值的列表的时间复杂度是 O(1)O(1)。在 Java 中，由于我们需要返回的 List 是一个接口，这里可以使用链表实现；而 C++ 或 Python 中，我们需要返回一个 vector 或 list，它不方便在头部插入元素（会增加时间开销），所以我们可以先用尾部插入的方法得到从上到下的层次遍历列表，然后再进行反转。

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是二叉树中的节点个数。每个节点访问一次，结果列表使用链表的结构时，在结果列表头部添加一层节点值的列表的时间复杂度是 O(1)，因此总时间复杂度是 O(n)。

- 空间复杂度：O(n)，其中 nn 是二叉树中的节点个数。空间复杂度取决于队列开销，队列中的节点个数不会超过 n。


