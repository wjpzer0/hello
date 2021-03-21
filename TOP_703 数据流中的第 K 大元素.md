# 题目

设计一个找到数据流中第 k 大元素的类（class）。注意是排序后的第 k 大元素，不是第 k 个不同的元素。

请实现 KthLargest 类：

- KthLargest(int k, int[] nums) 使用整数 k 和整数流 nums 初始化对象。
- int add(int val) 将 val 插入数据流 nums 后，返回当前数据流中第 k 大的元素。


示例：

```
输入：
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]
输出：
[null, 4, 5, 5, 8, 8]

解释：
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8
```

提示：
- $1 <= k <= 104$
- $0 <= nums.length <= 10^4$
- $-10^4 <= nums[i] <= 10^4$
- $-10^4 <= val <= 10^4$
- 最多调用 add 方法 $10^4$ 次
- 题目数据保证，在查找第 k 大元素时，数组中至少有 k 个元素
  

## 我的解法

```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.heap = []
        self.k = k
        for num in nums:
            self.heap.append(num)
        self.heap.sort(reverse=True)

    def add(self, val: int) -> int:
        self.heap.append(val)
        self.heap.sort(reverse=True)
        return self.heap[self.k - 1]


# Your KthLargest object will be instantiated and called as such:
# obj = KthLargest(k, nums)
# param_1 = obj.add(val)
```

效率较低，每一次调用的时间复杂度为O(nlog(n))

### 解题思路

https://mp.weixin.qq.com/s/FFsvWXiaZK96PtUg-mmtEw

本题是我们求在一个数据流中的第 K 大元素。所谓数据流，即是说我们写的算法需要支持 add() 函数；在力扣后台评测程序中会多次调用add()函数，每次调用都会向我们写的算法中添加一个元素。而题目要求的就是在每次 add() 之后，整个数据流（包括初始化的元素和所有 add 进来的元素）中的第 K 大元素。

先说一个最暴力的解法：我们底层数据结构使用数组实现，当每次调用 add() 函数时，向数组中添加一个元素，然后调用 sort() 函数进行排序，返回排序后数组的第 KK 个数字。该做法在每次调用 add() 函数时的时间复杂度为  ，该时间复杂度太高，当 K 很大 / add()调用次数太多的时候，一定会超时。

从上面的分析中，我们已经看出来了，使用数组的核心问题是：数组自身不带排序功能，只能用 sort() 函数，导致时间复杂度过高。

因此我们考虑使用自带排序功能的数据结构——堆。

在大根堆（图一）中，父节点的值比每一个子节点的值都要大。在小根堆（图二）中，父节点的值比每一个子节点的值都要小。

![image.png](https://pic.leetcode-cn.com/1612977776-oQiAdf-image.png)

**本题的操作步骤如下：**

1. 使用大小为 K 的小根堆，在初始化的时候，保证堆中的元素个数不超过 K 。
2. 在每次 add() 的时候，将新元素 push() 到堆中，如果此时堆中的元素超过了 K，那么需要把堆中的最小元素（堆顶）pop() 出来。
3. 此时堆中的最小元素（堆顶）就是整个数据流中的第 K 大元素。

**问答**：

1. 为什么使用小根堆？
- 因为我们需要在堆中保留数据流中的前 K 大元素，使用小根堆能保证每次调用堆的 pop() 函数时，从堆中删除的是堆中的最小的元素（堆顶）。
2. 为什么能保证堆顶元素是第 K 大元素？
- 因为小根堆中保留的一直是堆中的前 K 大的元素，堆的大小是 K，所以堆顶元素是第 K 大元素。
3. 每次 add() 的时间复杂度是多少？
- 每次 add() 时，调用了堆的 push() 和 pop() 方法，两个操作的时间复杂度都是 log(K).

#### 手写实现堆

https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/solution/python-dong-hua-shou-xie-shi-xian-dui-by-ypz2/

## 其他解法

### 优先队列

```java
class KthLargest {
    PriorityQueue<Integer> pq;
    int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        pq = new PriorityQueue<Integer>();
        for (int x : nums) {
            add(x);
        }
    }
    
    public int add(int val) {
        pq.offer(val);
        if (pq.size() > k) {
            pq.poll();
        }
        return pq.peek();
    }
}
```

```c
struct Heap {
    int* heap;
    int heapSize;
    bool (*cmp)(int, int);
};

void init(struct Heap* obj, int n, bool (*cmp)(int, int)) {
    obj->heap = malloc(sizeof(int) * (n + 1));
    obj->heapSize = 0;
    obj->cmp = cmp;
}

bool cmp(int a, int b) {
    return a > b;
}

void swap(int* a, int* b) {
    int tmp = *a;
    *a = *b, *b = tmp;
}

void push(struct Heap* obj, int x) {
    int p = ++(obj->heapSize), q = p >> 1;
    obj->heap[p] = x;
    while (q) {
        if (!obj->cmp(obj->heap[q], obj->heap[p])) {
            break;
        }
        swap(&(obj->heap[q]), &(obj->heap[p]));
        p = q, q = p >> 1;
    }
}

void pop(struct Heap* obj) {
    swap(&(obj->heap[1]), &(obj->heap[(obj->heapSize)--]));
    int p = 1, q = p << 1;
    while (q <= obj->heapSize) {
        if (q + 1 <= obj->heapSize) {
            if (obj->cmp(obj->heap[q], obj->heap[q + 1])) {
                q++;
            }
        }
        if (!obj->cmp(obj->heap[p], obj->heap[q])) {
            break;
        }
        swap(&(obj->heap[q]), &(obj->heap[p]));
        p = q, q = p << 1;
    }
}

int top(struct Heap* obj) {
    return obj->heap[1];
}

typedef struct {
    struct Heap* heap;
    int maxSize;
} KthLargest;

KthLargest* kthLargestCreate(int k, int* nums, int numsSize) {
    KthLargest* ret = malloc(sizeof(KthLargest));
    ret->heap = malloc(sizeof(struct Heap));
    init(ret->heap, k + 1, cmp);
    ret->maxSize = k;
    for (int i = 0; i < numsSize; i++) {
        kthLargestAdd(ret, nums[i]);
    }
    return ret;
}

int kthLargestAdd(KthLargest* obj, int val) {
    push(obj->heap, val);
    if (obj->heap->heapSize > obj->maxSize) {
        pop(obj->heap);
    }
    return top(obj->heap);
}

void kthLargestFree(KthLargest* obj) {
    free(obj->heap->heap);
    free(obj->heap);
    free(obj);
}。
```

我们可以使用一个大小为 k 的优先队列来存储前 k 大的元素，其中优先队列的队头为队列中最小的元素，也就是第 k 大的元素。

在单次插入的操作中，我们首先将元素 $\textit{val}$ 加入到优先队列中。如果此时优先队列的大小大于 k，我们需要将优先队列的队头元素弹出，以保证优先队列的大小为 k。

#### 复杂度分析

- 时间复杂度：
  - 初始化时间复杂度为：$O(n \log k)$)，其中 n 为初始化时 $\textit{nums}$ 的长度；
  - 单次插入时间复杂度为：O$(\log k)$。
- 空间复杂度：$O(k)$。需要使用优先队列存储前 kk 大的元素。

### heapq本身就是小顶堆

```python
import heapq


class KthLargest(object):

    def __init__(self, k, nums):
        """
        :type k: int
        :type nums: List[int]
        """
        # self k
        self.k = k
        self.heap = nums
        # heap其实就是个list
        heapq.heapify(self.heap)
        # 减小到k
        while len(self.heap) > k:
            heapq.heappop(self.heap)

    def add(self, val):
        """
        :type val: int
        :rtype: int
        """
        # 不过堆不够，则直接添加进去
        if len(self.heap) < self.k:
            heapq.heappush(self.heap, val)
        elif self.heap[0] < val:
            # 新的值更大，更新
            heapq.heapreplace(self.heap, val)

        return self.heap[0]
```

### 不清楚的类

https://docs.python.org/zh-cn/3.8/library/heapq.html

1、heapq.heapify 可以原地把一个 list 调整成堆
2、heapq.heappop 可以弹出堆顶，并重新调整
3、heapq.heappush 可以新增元素到堆中
4、heapq.heapreplace 可以替换堆顶元素，并调整下
5、为了维持为 K 的大小，初始化的时候可能需要删减，后面需要做处理就是如果不满 K 个就新增，否则做替换；
6、heapq 其实是对一个 list 做原地的处理，第一个元素就是最小的，直接返回就是最小的值

堆是一种特殊的树形结构，通常我们所说的堆的数据结构指的是完全二叉树，并且根节点的值小于等于该节点所有子节点的值

![img](https://images2018.cnblogs.com/blog/1117865/201803/1117865-20180301134905272-2030985781.png)

#### 常用方法

| heappush(heap,item)                      | 往堆中插入一条新的值                            |
| ---------------------------------------- | ----------------------------------------------- |
| heappop(heap)                            | 从堆中弹出最小值                                |
| heapreplace(heap,item)                   | 从堆中弹出最小值，并往堆中插入item              |
| heappushpop(heap,item)                   | Python3中的heappushpop更高级                    |
| heapify(x)                               | 以线性时间将一个列表转化为堆                    |
| merge(*iterables,key=None,reverse=False) | 合并对个堆，然后输出                            |
| nlargest(n,iterable,key=None)            | 返回可枚举对象中的n个最大值并返回一个结果集list |
| nsmallest(n,iterable,key=None)           | 返回可枚举对象中的n个最小值并返回一个结果集list |