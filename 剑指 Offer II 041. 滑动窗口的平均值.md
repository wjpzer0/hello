# 题目

给定一个整数数据流和一个窗口大小，根据该滑动窗口的大小，计算滑动窗口里所有数字的平均值。

实现 `MovingAverage` 类：

- `MovingAverage(int size)` 用窗口大小 `size` 初始化对象。
- `double next(int val)` 成员函数 `next` 每次调用的时候都会往滑动窗口增加一个整数，请计算并返回数据流中最后 `size` 个值的移动平均值，即滑动窗口里所有数字的平均值。

**示例：**

```
输入：
inputs = ["MovingAverage", "next", "next", "next", "next"]
inputs = [[3], [1], [10], [3], [5]]
输出：
[null, 1.0, 5.5, 4.66667, 6.0]

解释：
MovingAverage movingAverage = new MovingAverage(3);
movingAverage.next(1); // 返回 1.0 = 1 / 1
movingAverage.next(10); // 返回 5.5 = (1 + 10) / 2
movingAverage.next(3); // 返回 4.66667 = (1 + 10 + 3) / 3
movingAverage.next(5); // 返回 6.0 = (10 + 3 + 5) / 3
```

**提示：**

- `1 <= size <= 1000`
- $-10^5 <= val <= 10^5$
- 最多调用 `next` 方法 $10^4 $次

## 我的解法

代码不够简洁！思路有些混乱！

```python
class MovingAverage:

    def __init__(self, size: int):
        """
        Initialize your data structure here.
        """
        self.size = size
        self.nums = []
        self.sum = 0

    def next(self, val: int) -> float:
        self.nums.append(val)
        self.sum += val
        if len(self.nums) > self.size:
            self.sum -= self.nums[0]
            self.nums.pop(0)
        else:
            return self.sum / len(self.nums)
        return self.sum / self.size

# Your MovingAverage object will be instantiated and called as such:
# obj = MovingAverage(size)
# param_1 = obj.next(val)
```

## 其他解法

### 队列

```c++
class MovingAverage {
public:
    MovingAverage(int size) {
        this->size = size;
        this->sum = 0.0;
    }
    
    double next(int val) {
        if (qu.size() == size) {
            sum -= qu.front();
            qu.pop();
        }
        qu.emplace(val);
        sum += val;
        return sum / qu.size();
    }
private:
    int size;
    double sum;
    queue<int> qu;
};
```

```java
class MovingAverage {
    Queue<Integer> queue;
    int size;
    double sum;

    public MovingAverage(int size) {
        queue = new ArrayDeque<Integer>();
        this.size = size;
        sum = 0;
    }

    public double next(int val) {
        if (queue.size() == size) {
            sum -= queue.poll();
        }
        queue.offer(val);
        sum += val;
        return sum / queue.size();
    }
}
```

```python
class MovingAverage:
    def __init__(self, size: int):
        self.size = size
        self.sum = 0
        self.q = deque()

    def next(self, val: int) -> float:
        if len(self.q) == self.size:
            self.sum -= self.q.popleft()
        self.sum += val
        self.q.append(val)
        return self.sum / len(self.q)
```

这道题要求根据给定的数据流计算滑动窗口中所有数字的平均值，滑动窗口的大小为给定的参数 $\textit{size}$。当数据流中的数字个数不超过滑动窗口的大小时，计算数据流中的所有数字的平均值；当数据流中的数字个数超过滑动窗口的大小时，只计算滑动窗口中的数字的平均值，数据流中更早的数字被移出滑动窗口。

由于数字进入滑动窗口和移出滑动窗口的规则符合先进先出，因此可以使用队列存储滑动窗口中的数字，同时维护滑动窗口的大小以及滑动窗口的数字之和。

初始时，队列为空，滑动窗口的大小设为给定的参数 $\textit{size}$，滑动窗口的数字之和为 0。

每次调用 $\texttt{next}$时，需要将 $\textit{val}$添加到滑动窗口中，同时确保滑动窗口中的数字个数不超过 $\textit{size}$，如果数字个数超过 $\textit{size}$则需要将多余的数字移除，在添加和移除数字的同时需要更新滑动窗口的数字之和。由于每次调用只会将一个数字添加到滑动窗口中，因此每次调用最多只需要将一个多余的数字移除。具体操作如下。

1. 如果队列中的数字个数等于滑动窗口的大小，则移除队首的数字，将移除的数字从滑动窗口的数字之和中减去。如果队列中的数字个数小于滑动窗口的大小，则不移除队首的数字。

2. 将 $\textit{val}$添加到队列中，并加到滑动窗口的数字之和中。

3. 计算滑动窗口的数字之和与队列中的数字个数之商，即为滑动窗口中所有数字的平均值。


#### 复杂度分析

- 时间复杂度：初始化和每次调用 $\texttt{next}$的时间复杂度都是 O(1)。

- 空间复杂度：$O(\textit{size})$，其中 $\textit{size}$是滑动窗口的大小。空间复杂度主要取决于队列，队列中的数字个数不超过 $\textit{size}$。


