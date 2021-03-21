# 题目

给你单链表的头节点 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)


示例 1：

```
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```


示例 2：

```
输入：head = [5], left = 1, right = 1
输出：[5]
```


提示：

- 链表中节点数目为 n
- 1 <= n <= 500
- -500 <= Node.val <= 500
- 1 <= left <= right <= n

## 我的解法

未解出，想到了递归，但写不出来

## 其他解法

### 穿针引线

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        def reverse_linked_list(head: ListNode):
            # 也可以使用递归反转一个链表
            pre = None
            cur = head
            while cur:
                next = cur.next
                cur.next = pre
                pre = cur
                cur = next

        # 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
        dummy_node = ListNode(-1)
        dummy_node.next = head
        pre = dummy_node
        # 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
        # 建议写在 for 循环里，语义清晰
        for _ in range(left - 1):
            pre = pre.next

        # 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
        right_node = pre
        for _ in range(right - left + 1):
            right_node = right_node.next
        # 第 3 步：切断出一个子链表（截取链表）
        left_node = pre.next
        curr = right_node.next

        # 注意：切断链接
        pre.next = None
        right_node.next = None

        # 第 4 步：同第 206 题，反转链表的子区间
        reverse_linked_list(left_node)
        # 第 5 步：接回到原来的链表中
        pre.next = right_node
        left_node.next = curr
        return dummy_node.next
```

我们以下图中黄色区域的链表反转为例。

![image.png](https://pic.leetcode-cn.com/1615105129-iUPoGi-image.png)

使用TOP_206 反转链表的解法，反转 left 到 right 部分以后，再拼接起来。我们还需要记录 left 的前一个节点，和 right 的后一个节点。如图所示：

![image.png](https://pic.leetcode-cn.com/1615105150-pfWiGq-image.png)

#### 算法步骤：

- 第 1 步：先将待反转的区域反转；
- 第 2 步：把 pre 的 next 指针指向反转以后的链表头节点，把反转以后的链表的尾节点的 next 指针指向 succ。

![image.png](https://pic.leetcode-cn.com/1615105168-ZQRZew-image.png)


说明：编码细节我们不在题解中介绍了，请见下方代码。思路想明白以后，编码不是一件很难的事情。这里要提醒大家的是，链接什么时候切断，什么时候补上去，先后顺序一定要想清楚，如果想不清楚，可以在纸上模拟，让思路清晰。

**复杂度分析**

- 时间复杂度：O(N)，其中 N是链表总节点数。最坏情况下，需要遍历整个链表。
- 空间复杂度：O(1)。只使用到常数个变量。

### 一次遍历「穿针引线」反转链表

```python
class Solution:
    def reverseBetween(self, head: ListNode, left: int, right: int) -> ListNode:
        # 设置 dummyNode 是这一类问题的一般做法
        dummy_node = ListNode(-1)
        dummy_node.next = head
        pre = dummy_node
        for _ in range(left - 1):
            pre = pre.next

        cur = pre.next
        for _ in range(right - left):
            next = cur.next
            cur.next = next.next
            next.next = pre.next
            pre.next = next
        return dummy_node.next
```

方法一的缺点是：如果 left 和 right 的区域很大，恰好是链表的头节点和尾节点时，找到 left 和 right 需要遍历一次，反转它们之间的链表还需要遍历一次，虽然总的时间复杂度为 O(N)O(N)，但遍历了链表 22 次，可不可以只遍历一次呢？答案是可以的。我们依然画图进行说明。

我们依然以方法一的示例为例进行说明。

![image.png](https://pic.leetcode-cn.com/1615105232-cvTINs-image.png)

整体思想是：在需要反转的区间里，每遍历到一个节点，让这个新节点来到反转部分的起始位置。下面的图展示了整个流程。

![image.png](https://pic.leetcode-cn.com/1615105242-ZHlvOn-image.png)

下面我们具体解释如何实现。使用三个指针变量 pre、curr、next 来记录反转的过程中需要的变量，它们的意义如下：

- curr：指向待反转区域的第一个节点 left；
- next：永远指向 curr 的下一个节点，循环过程中，curr 变化以后 next 会变化；
- pre：永远指向待反转区域的第一个节点 left 的前一个节点，在循环过程中不变。

第 1 步，我们使用 ①、②、③ 标注「穿针引线」的步骤。

![image.png](https://pic.leetcode-cn.com/1615105296-bmiPxl-image.png)

操作步骤：

- 先将 curr 的下一个节点记录为 next；
- 执行操作 ①：把 curr 的下一个节点指向 next 的下一个节点；
- 执行操作 ②：把 next 的下一个节点指向 pre 的下一个节点；
- 执行操作 ③：把 pre 的下一个节点指向 next。

第 1 步完成以后「拉直」的效果如下：

![image.png](https://pic.leetcode-cn.com/1615105340-UBnTBZ-image.png)

第 2 步，同理。同样需要注意 「穿针引线」操作的先后顺序。

![image.png](https://pic.leetcode-cn.com/1615105353-PsCmzb-image.png)

第 2 步完成以后「拉直」的效果如下：

![image.png](https://pic.leetcode-cn.com/1615105364-aDIFqy-image.png)

第 3 步，同理。

![image.png](https://pic.leetcode-cn.com/1615105376-jIyGwv-image.png)

第 3 步完成以后「拉直」的效果如下：

![image.png](https://pic.leetcode-cn.com/1615105395-EJQnMe-image.png)

#### 复杂度分析：

- 时间复杂度：O(N)，其中 N 是链表总节点数。最多只遍历了链表一次，就完成了反转。

- 空间复杂度：O(1)。只使用到常数个变量。


