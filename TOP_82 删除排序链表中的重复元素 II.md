# 题目

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

示例 1:

```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```


示例 2:

```
输入: 1->1->1->2->3
输出: 2->3
```

## 我的解法

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        newHead = ListNode()
        newHead.next = head
        after = newHead
        point = head
        if not head:
            return head
        front = head.next
        flag = False
        while front:
            if point.val == front.val:
                front = front.next
                flag = True
            else:
                if flag:
                    after.next = front
                    point = front
                    front = front.next
                    flag = False
                else:
                    after = after.next
                    point = point.next
                    front = front.next
        if flag:
            after.next = front
        return newHead.next
```

较为繁琐，可精简。

## 其他解法

### 一次遍历

```python
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        if not head:
            return head
        
        dummy = ListNode(0, head)

        cur = dummy
        while cur.next and cur.next.next:
            if cur.next.val == cur.next.next.val:
                x = cur.next.val
                while cur.next and cur.next.val == x:
                    cur.next = cur.next.next
            else:
                cur = cur.next

        return dummy.next
```

#### 思路与算法

由于给定的链表是排好序的，因此重复的元素在链表中出现的位置是连续的，因此我们只需要对链表进行一次遍历，就可以删除重复的元素。由于链表的头节点可能会被删除，因此我们需要额外使用一个哑节点（dummy node）指向链表的头节点。

具体地，我们从指针 cur 指向链表的哑节点，随后开始对链表进行遍历。如果当前 cur.next 与 cur.next.next 对应的元素相同，那么我们就需要将 cur.next 以及所有后面拥有相同元素值的链表节点全部删除。我们记下这个元素值 x，随后不断将 cur.next 从链表中移除，直到 cur.next 为空节点或者其元素值不等于 x 为止。此时，我们将链表中所有元素值为 x 的节点全部删除。

如果当前 cur.next 与 cur.next.next 对应的元素不相同，那么说明链表中只有一个元素值为 cur.next 的节点，那么我们就可以将 cur 指向 cur.next。

当遍历完整个链表之后，我们返回链表的的哑节点的下一个节点 dummy.next 即可。

细节

需要注意 cur.next 以及 cur.next.next 可能为空节点，如果不加以判断，可能会产生运行错误。

代码

注意下面 C++ 代码中并没有释放被删除的链表节点以及哑节点的空间。如果在面试中遇到本题，读者需要针对这一细节与面试官进行沟通。

**复杂度分析**

- 时间复杂度：O(n)，其中 n是链表的长度。
- 空间复杂度：O(1)。