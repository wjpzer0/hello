# 题目

给你一个链表的头节点 head ，旋转链表，将链表每个节点向右移动 k 个位置。

 

示例 1：

![img](https://assets.leetcode.com/uploads/2020/11/13/rotate1.jpg)

```
输入：head = [1,2,3,4,5], k = 2
输出：[4,5,1,2,3]
```

示例 2：

![img](https://assets.leetcode.com/uploads/2020/11/13/roate2.jpg)

```
输入：head = [0,1,2], k = 4
输出：[2,0,1]
```


提示：

- 链表中节点的数目在范围 [0, 500] 内
- -100 <= Node.val <= 100
- $0 <= k <= 2 * 10^9$

## 我的解法

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:
        if k == 0 or not head or not head.next:
            return head
        #链表为空，为一个节点，翻转定位为0，返回原链表
        lenNode = 1
        end = head
        while end.next:
            lenNode += 1
            end = end.next
        k = k % lenNode
        rk = lenNode - k
        #定位需要翻转的位置
        if rk == lenNode:
            return head
        #如果翻转的位置在链表的最后，则不变
        start = head
        for i in range(rk-1):
            start = start.next
        #移动到翻转位置的前一个节点
        newHead = ListNode(next=start.next)
        start.next = end.next
        end.next = head
        #翻转
        return newHead.next
```

代码不简洁，部分细节没有注意到，查看题解后解决。

### 解题思路

题意：将链表每个节点向右移动 kk 个位置，相当于把链表的后面 k % len  个节点移到链表的最前面。（len 为 链表长度）

 所以本题的步骤：

1. 求链表长度；
2. 找出倒数第 k+1 个节点；
3. 链表重整：将链表的倒数第 k+1 个节点和倒数第 k 个节点断开，并把后半部分拼接到链表的头部。

1. #### 求链表长度

  求链表长度应该是链表最基本的题型了，直接用一个指针 curcur ，开始时指向链表的头 headhead，一直向后移动到 curcur 为空时，经历的链表节点数就是链表长度。

2. #### 找出倒数第 k+1 个节点

思路是：

1. 两个指针 slow 和 fast 值距离是 kk，先让 fast 指向链表的第 k + 1 个节点，slow 指向第 1 个节点；
2. 然后 slow 和 fast 同时向后移动，当 fast 移动到链表的最后一个节点的时候，那么 slow 指向链表的倒数第 k + 1 个节点。



3. #### 链表重整

  重整操作的步骤是：

1. newHead 是新链表的头部，它应该是原链表倒数第 k 个节点，即 slow.next；
2. slow 需要跟 slow.next 断开；
3. fast 是老链表的结尾，将 fast.next 设置为老链表的开头，实现首尾相接。

可以用下面的图来帮助理解：链表是1->2->3->4->5->null， k=2。图中 ① 是让 fast 指向第 k+1k+1 个节点；图中 ② 是 fast 和 slow 同时后移至链表结尾，此时 slow指向链表倒数第 k+1k+1 个节点；图中 ③ 是链表重整的操作。

![61.001.jpeg](https://pic.leetcode-cn.com/1616779165-fTkdtp-61.001.jpeg)

## 其他解法

### 闭合为环

```python
class Solution:
    def rotateRight(self, head: ListNode, k: int) -> ListNode:
        if k == 0 or not head or not head.next:
            return head
        
        n = 1
        cur = head
        while cur.next:
            cur = cur.next
            n += 1
        
        if (add := n - k % n) == n:
            return head
        
        cur.next = head
        while add:
            cur = cur.next
            add -= 1
        
        ret = cur.next
        cur.next = None
        return ret
```

#### 思路及算法

记给定链表的长度为 n，注意到当向右移动的次数 k≥n 时，我们仅需要向右移动 k mod n 次即可。因为每 n 次移动都会让链表变为原状。这样我们可以知道，新链表的最后一个节点为原链表的第 (n−1)−(kmodn) 个节点（从 0 开始计数）。

这样，我们可以先将给定的链表连接成环，然后将指定位置断开。

具体代码中，我们首先计算出链表的长度 nn，并找到该链表的末尾节点，将其与头节点相连。这样就得到了闭合为环的链表。然后我们找到新链表的最后一个节点（即原链表的第 (n−1)−(kmodn) 个节点），将当前闭合为环的链表断开，即可得到我们所需要的结果。

特别地，当链表长度不大于 1，或者 k 为 n 的倍数时，新链表将与原链表相同，我们无需进行任何处理。

**复杂度分析**

- 时间复杂度：O(n)，最坏情况下，我们需要遍历该链表两次。
- 空间复杂度：O(1)，我们只需要常数的空间存储若干变量。