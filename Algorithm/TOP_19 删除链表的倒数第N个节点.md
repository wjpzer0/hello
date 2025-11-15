#### 题目

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例：

给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？

#### 我的解法

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        nullhead = ListNode
        nullhead.next = head
        firsthead = nullhead
        nexthead =  nullhead
        i = 0
        while i <= n:
            firsthead = firsthead.next
            i += 1
        while firsthead != None:
            firsthead = firsthead.next
            nexthead = nexthead.next
        midhead = nexthead
        nexthead.next = midhead.next.next
        return nullhead.next

```

双指针，参考了部分题解

#### 其他解法

###### 双指针

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {    
        ListNode pre = new ListNode(0);
        pre.next = head;
        ListNode start = pre, end = pre;
        while(n != 0) {
            start = start.next;
            n--;
        }
        while(start.next != null) {
            start = start.next;
            end = end.next;
        }
        end.next = end.next.next;
        return pre.next;
    }
}
```

我们可以设想假设设定了双指针 p 和 q 的话，当 q 指向末尾的 NULL，p 与 q 之间相隔的元素个数为 n 时，那么删除掉 p 的下一个指针就完成了要求。

- 设置虚拟节点 dummyHead 指向 head
- 设定双指针 p 和 q，初始都指向虚拟节点 dummyHead
- 移动 q，直到 p 与 q 之间相隔的元素个数为 n
- 同时移动 p 与 q，直到 q 指向的为 NULL
- 将 p 的下一个节点指向下下个节点

![img](https://pic.leetcode-cn.com/cc43daa8cbb755373ce4c5cd10c44066dc770a34a6d2913a52f8047cbf5e6e56-file_1559548337458)

**复杂度分析**

- 时间复杂度：O(L)，其中 L*L* 是链表的长度。
- 空间复杂度：O(1)。

###### 递归

```python
class Solution:
    def removeNthFromEnd(self, head, n):
        global i 
        if head is None:
            i=0
            return None
        head.next = self.removeNthFromEnd(head.next,n)
        i+=1
        return head.next if i==n else head
```

###### 计算链表长度

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        def getLength(head: ListNode) -> int:
            length = 0
            while head:
                length += 1
                head = head.next
            return length
        
        dummy = ListNode(0, head)
        length = getLength(head)
        cur = dummy
        for i in range(1, length - n + 1):
            cur = cur.next
        cur.next = cur.next.next
        return dummy.next
```

**思路与算法**

一种容易想到的方法是，我们首先从头节点开始对链表进行一次遍历，得到链表的长度 LL。随后我们再从头节点开始对链表进行一次遍历，当遍历到第 L-n+1L−n+1 个节点时，它就是我们需要删除的节点。

为了与题目中的 nn 保持一致，节点的编号从 11 开始，头节点为编号 11 的节点。

为了方便删除操作，我们可以从哑节点开始遍历 L-n+1L−n+1 个节点。当遍历到第 L-n+1L−n+1 个节点时，它的下一个节点就是我们需要删除的节点，这样我们只需要修改一次指针，就能完成删除操作。

![p1](https://assets.leetcode-cn.com/solution-static/19/p1.png)

**复杂度分析**

- 时间复杂度：O(L)，其中 L 是链表的长度。
- 空间复杂度：O(1)

###### 栈

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(0, head)
        stack = list()
        cur = dummy
        while cur:
            stack.append(cur)
            cur = cur.next
        
        for i in range(n):
            stack.pop()

        prev = stack[-1]
        prev.next = prev.next.next
        return dummy.next
```

**思路与算法**

我们也可以在遍历链表的同时将所有节点依次入栈。根据栈「先进后出」的原则，我们弹出栈的第 nn 个节点就是需要删除的节点，并且目前栈顶的节点就是待删除节点的前驱节点。这样一来，删除操作就变得十分方便了。

![img](https://assets.leetcode-cn.com/solution-static/19/6.png)

![img](https://assets.leetcode-cn.com/solution-static/19/7.png)

![img](https://assets.leetcode-cn.com/solution-static/19/8.png)

![img](https://assets.leetcode-cn.com/solution-static/19/10.png)