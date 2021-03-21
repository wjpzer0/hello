#### 题目

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

 ![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

示例 1：

```
输入：head = [1,2,3,4]
输出：[2,1,4,3]
```


示例 2：

```
输入：head = []
输出：[]
```


示例 3：

```
输入：head = [1]
输出：[1]
```


提示：

- 链表中节点的数目在范围 [0, 100] 内
- 0 <= Node.val <= 100

#### 我的解法

未解出，迭代没思考到需要再填一个头，递归没考虑

#### 其他解法

###### 迭代

```python
class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        thead = ListNode(-1)
        thead.next = head
        c = thead
        while c.next and c.next.next:
            a, b=c.next, c.next.next
            c.next, a.next = b, b.next
            b.next = a
            c = c.next.next
        return thead.next
```

```java
class Solution {
	public ListNode swapPairs(ListNode head) {
		//增加一个特殊节点方便处理
		ListNode p = new ListNode(-1);
		p.next = head;
		//创建a，b两个指针，这里还需要一个tmp指针
		ListNode a = p;
		ListNode b = p;
		ListNode tmp = p;
		while(b!=null && b.next!=null && b.next.next!=null) {
			//a前进一位，b前进两位
			a = a.next;
			b = b.next.next;
			//这步很关键，tmp指针用来处理边界条件的
			//假设链表是1->2->3->4，a指向1，b指向2
			//改变a和b的指向，于是就变成2->1，但是1指向谁呢？
			//1是不能指向2的next，1应该指向4，而循环迭代的时候一次处理2个节点
			//1和2的关系弄清楚了，3和4的关系也能弄清楚，但需要一个指针来处理
			//2->1，4->3的关系，tmp指针就是干这个用的
			tmp.next = b;
			a.next = b.next;
			b.next = a;
			//现在链表就变成2->1->3->4
			//tmp和b都指向1节点，等下次迭代的时候
			//a就变成3，b就变成4，然后tmp就指向b，也就是1指向4
			tmp = a;
			b = a;
		}
		return p.next;
	}
}
```

先添加一个空头，再交换
交换过程：

![a.jpg](https://pic.leetcode-cn.com/43254846f029b4814a6c9a139e4f9f89833ac54803ea50b24feb35210631f88b-a.jpg)

###### 递归

```python
class Solution(object):
	def swapPairs(self, head):
		# 递归的终止条件
		if not (head and head.next):
			return head
		# 假设链表是 1->2->3->4
		# 这句就先保存节点2
		tmp = head.next
		# 继续递归，处理节点3->4
		# 当递归结束返回后，就变成了4->3
		# 于是head节点就指向了4，变成1->4->3
		head.next = self.swapPairs(tmp.next)
		# 将2节点指向1
		tmp.next = head
		return tmp
```

下面以1->2->....来说明：
终止条件：当前节点为null，或者下一个节点为 nullA
函数内：将 2 指向 1，1 指向下一层的递归函数，最后返回节点 2
下面中t就表示函数内的临时节点 tmp，图中节点 1，节点 3 指向的一个片空白，这表示引用关系还没真正确定，要等下一层递归函数返回后，才能真正确定最终指向。

![递归.gif](https://pic.leetcode-cn.com/7ae491344608971d449add1e069aa143ee264b07a9bb8a1950e08dcf8d8a1ff9-%E9%80%92%E5%BD%92.gif)

我们来一一分析下，假设链表总长是偶数，那么递归函数执行到终止条件时，head 就等于 null。如果链表链表总长是偶数，那么递归函数执行到终止条件时，head.next 就等于 null。
递归函数内，我们要改变 1->2 的指向，将其改为 2->1。
那后面的节点怎么办呢？不用担心，这是由下一层递归函数来解决。下一层递归函数返回后的节点是 4，就是4->3->...这样的了，也就是后面的节点都已经串联好了。所以我们只需要将 1 节点指向 4 就可以啦。

###### 利用stack

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        if not (head and head.next):
            return head
        p = ListNode(-1)
        # 用stack保存每次迭代的两个节点
        # head指向新的p节点，函数结束时返回head.next即可
        cur,head,stack = head,p,[]
        while cur and cur.next:
            # 将两个节点放入stack中
            stack.append(cur)
            stack.append(cur.next)
            # 当前节点往前走两步
            cur = cur.next.next
            # 从stack中弹出两个节点，然后用p节点指向新弹出的两个节点
            p.next = stack.pop()
            p.next.next = stack.pop()
            p = p.next.next
            # 注意边界条件，当链表长度是奇数时，cur就不为空	
        if cur:
            p.next = cur
        else:
            p.next = None
        return head.next
```

我们利用一个 stack，然后不断迭代链表，每次取出两个节点放入 stack 中，再从 stack 中拿出两个节点。
借助 stack 后进先出的特点，放进去的时候是 1,2 。拿出来的时候就是 2，1 两个节点了。
再把这两个节点串联起来，重复这个逻辑遍历完整个链表，就可以做到两两反转的效果了。
虽然用到了 stack，但因为只存了两个元素，所以空间复杂度还是 O(1)，时间复杂度是 O(n)

![stack方式.gif](https://pic.leetcode-cn.com/4ddf5a9e08578f875313a617e601746789072136df5e7722d5236f51cd7baf76-stack%E6%96%B9%E5%BC%8F.gif)