# 题目

给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

示例 1:

![img](https://assets.leetcode.com/uploads/2021/01/04/list1.jpg)

```
输入: 1->1->2
输出: 1->2
```

示例 2:

![img](https://assets.leetcode.com/uploads/2021/01/04/list2.jpg)

```
输入: 1->1->2->3->3
输出: 1->2->3
```



#### 我的解法

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head == None or head.next == None:
            return head
        hNode = head
        nNode = head.next
        hNode.next = None
        start = hNode
        while nNode:
            if hNode.val == nNode.val:
                nNode = nNode.next
            else:
                hNode.next = nNode
                nNode = nNode.next
                hNode = hNode.next
                hNode.next = None
        return start
```

有思路，但是编写混乱

再次编写

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head:
            return head
        pre =head.next
        point = ListNode(next=head)
        start = point
        flag = False
        while pre:
            if point.next.val == pre.val:
                pre = pre.next
                flag = True
            else:
                point = point.next
                point.next = pre
                pre = pre.next
        if flag:
            point = point.next
            point.next = None
        return start.next
```



## 其他解法

### 一次遍历

#### 思路与算法

由于给定的链表是排好序的，因此重复的元素在链表中出现的位置是连续的，因此我们只需要对链表进行一次遍历，就可以删除重复的元素。

具体地，我们从指针 cur 指向链表的头节点，随后开始对链表进行遍历。如果当前 cur 与 cur.next 对应的元素相同，那么我们就将 cur.next 从链表中移除；否则说明链表中已经不存在其它与 cur 对应的元素相同的节点，因此可以将 cur 指向 cur.next。

当遍历完整个链表之后，我们返回链表的头节点即可。

细节

当我们遍历到链表的最后一个节点时，cur.next 为空节点，如果不加以判断，访问 cur.next 对应的元素会产生运行错误。因此我们只需要遍历到链表的最后一个节点，而不需要遍历完整个链表。

代码

注意下面 C++ 代码中并没有释放被删除的链表节点的空间。如果在面试中遇到本题，读者需要针对这一细节与面试官进行沟通。

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head:
            return head

        cur = head
        while cur.next:
            if cur.val == cur.next.val:
                cur.next = cur.next.next
            else:
                cur = cur.next

        return head
```



```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        node=head
        while node and node.next:
            if node.val==node.next.val:
                node.next=node.next.next
            else:
                node=node.next
        return head
```

###### 递归

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        
        child = self.deleteDuplicates(head.next)
        if child and head.val == child.val:
            head.next = child.next
            child.next = None
            
        return head
```

