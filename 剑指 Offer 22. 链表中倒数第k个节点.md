# 题目

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6`。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

## 我的解法

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        int size = 0;
        ListNode* meause = head;
        while (meause)
        {
            size++;
            meause = meause->next;
        }
        
        int pointS = size - k;
        ListNode* ans = head;
        for (int i = 0; i < pointS; i++)
        {
            ans = ans->next;
        }

        return ans;
    }
};
```

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        size = 0
        measure = head
        while measure:
            size += 1
            measure = measure.next
        pointS = size - k 
        ans = head
        for _ in range(pointS):
            ans = ans.next
        return ans
```

较为简单的解法，必须遍历整个链表！

## 其他解法

### 顺序查找

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        int n = 0;
        ListNode* node = nullptr;

        for (node = head; node; node = node->next) {
            n++;
        }
        for (node = head; n > k; n--) {
            node = node->next;
        }
      
        return node;
    }
};
```

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int n = 0;
        ListNode node = null;

        for (node = head; node != null; node = node.next) {
            n++;
        }
        for (node = head; n > k; n--) {
            node = node.next;
        }

        return node;
    }
}
```

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        node, n = head, 0  
        while node:
            node = node.next
            n += 1

        node = head
        for _ in range(n-k):
            node = node.next
        
        return node  
```

#### 思路与算法

最简单直接的方法即为顺序查找，假设当前链表的长度为 n，则我们知道链表的倒数第 k 个节点即为正数第 n - k 个节点，此时我们只需要顺序遍历到链表的第 n - k 个节点即为倒数第 k 个节点。

我们首先求出链表的长度 n，然后顺序遍历到链表的第 n - k 个节点返回即可。

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为链表的长度。需要两次遍历。
- 空间复杂度：O(1)。

### 双指针

```c++
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* fast = head;
        ListNode* slow = head;

        while (fast && k > 0) {
            fast = fast->next;
            k--;
        }
        while (fast) {
            fast = fast->next;
            slow = slow->next;
        }

        return slow;
    }
};
```

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        ListNode slow = head;

        while (fast != null && k > 0) {
            fast = fast.next;
            k--;
        }
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }

        return slow;
    }
}
```

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        fast, slow = head, head

        while fast and k > 0:
            fast, k = fast.next, k - 1
        while fast:
            fast,slow = fast.next,slow.next
        
        return slow
```

#### 思路与算法

快慢指针的思想。我们将第一个指针 $\textit{fast}$ 指向链表的第 k + 1 个节点，第二个指针 $\textit{slow}$ 指向链表的第一个节点，此时指针 $\textit{fast}$ 与$ \textit{slow}$ 二者之间刚好间隔 k 个节点。此时两个指针同步向后走，当第一个指针 $\textit{fast}$ 走到链表的尾部空节点时，则此时 $\textit{slow}$ 指针刚好指向链表的倒数第k个节点。

- 我们首先将 $\textit{fast}$ 指向链表的头节点，然后向后走 k 步，则此时 $\textit{fast}$ 指针刚好指向链表的第 k + 1 个节点。

- 我们首先将 $\textit{slow}$ 指向链表的头节点，同时 $\textit{slow}$ 与 $\textit{fast}$ 同步向后走，当 $\textit{fast}$ 指针指向链表的尾部空节点时，则此时返回 $\textit{slow}$ 所指向的节点即可。


#### 复杂度分析

- 时间复杂度：O(n)，其中 n 为链表的长度。我们使用快慢指针，只需要一次遍历即可，复杂度为 O(n)。

- 空间复杂度：O(1)。


