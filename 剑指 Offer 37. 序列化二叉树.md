# 题目

请实现两个函数，分别用来序列化和反序列化二叉树。

你需要设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

**提示：**输入输出格式与 LeetCode 目前使用的方式一致，详情请参阅 [LeetCode 序列化二叉树的格式](https://leetcode-cn.com/faq/#binary-tree)。你并非必须采取这种方式，你也可以采用其他的方法解决这个问题。

 

**示例：**

![img](https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg)

```
输入：root = [1,2,3,null,null,4,5]
输出：[1,2,3,null,null,4,5]
```

 

注意：本题与主站 297 题相同：https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/

## 我的解法

未解出，没有理解序列化二叉树的格式话，一脸懵逼！

## 其他解法

### 层序遍历 BFS

```python
class Codec:
    def serialize(self, root):
        if not root: return "[]"
        queue = collections.deque()
        queue.append(root)
        res = []
        while queue:
            node = queue.popleft()
            if node:
                res.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else: res.append("null")
        return '[' + ','.join(res) + ']'

    def deserialize(self, data):
        if data == "[]": return
        vals, i = data[1:-1].split(','), 1
        root = TreeNode(int(vals[0]))
        queue = collections.deque()
        queue.append(root)
        while queue:
            node = queue.popleft()
            if vals[i] != "null":
                node.left = TreeNode(int(vals[i]))
                queue.append(node.left)
            i += 1
            if vals[i] != "null":
                node.right = TreeNode(int(vals[i]))
                queue.append(node.right)
            i += 1
        return root
```

#### 解题思路：

通常使用的前序、中序、后序、层序遍历记录的二叉树的信息不完整，即唯一的输出序列可能对应着多种二叉树可能性。题目要求的 序列化 和 反序列化 是 可逆操作 。因此，序列化的字符串应携带 完整的二叉树信息 。

观察题目示例，序列化的字符串实际上是二叉树的 “层序遍历”（BFS）结果，本文也采用层序遍历。

为完整表示二叉树，考虑将叶节点下的 null 也记录。在此基础上，对于列表中任意某节点 node ，其左子节点 node.left 和右子节点 node.right 在序列中的位置都是 唯一确定 的。如下图所示：

![Picture1.png](https://pic.leetcode-cn.com/1603117385-ehAGsP-Picture1.png)

上图规律可总结为下表：

| `node.val` | `node` 的索引 | `node.left` 的索引 | `node.right` 的索引 |
| ---------- | ------------- | ------------------ | ------------------- |
| 1          | 0             | 1                  | 2                   |
| 2          | 1             | 3                  | 4                   |
| 3          | 2             | 5                  | 6                   |
| 4          | 5             | 7                  | 8                   |
| 5          | 6             | 9                  | 10                  |


设 m 为列表区间 $[0, n]$ 中的 null 节点个数，则可总结出根节点、左子节点、右子节点的列表索引的递推公式：

| `node.val`   | `node` 的列表索引 | `node.left` 的列表索引 | `node.right` 的列表索引 |
| ------------ | ----------------- | ---------------------- | ----------------------- |
| $\neq$`null` | n                 | 2(*n*−*m*)+1           | 2(*n*−*m*)+2            |
| = `null`     | n                 | 无                     | 无                      |

序列化 使用层序遍历实现。反序列化 通过以上递推公式反推各节点在序列中的索引，进而实现。

#### 序列化 Serialize ：

借助队列，对二叉树做层序遍历，并将越过叶节点的 null 也打印出来。

1. 算法流程：
   特例处理： 若 root 为空，则直接返回空列表 "[]" ；
2. 初始化： 队列 queue （包含根节点 root ）；序列化列表 res ；
3. 层序遍历： 当 queue 为空时跳出；
	1. 节点出队，记为 node ；
	2. 若 node 不为空：① 打印字符串 node.val ，② 将左、右子节点加入 queue ；
	3. 否则（若 node 为空）：打印字符串 "null" ；
7. 返回值： 拼接列表，用 ',' 隔开，首尾添加中括号；

![img](https://pic.leetcode-cn.com/1603117385-wmoUSQ-Picture2.png)
![img](https://pic.leetcode-cn.com/1603117385-UEgpOH-Picture3.png)
![img](https://pic.leetcode-cn.com/1603117385-yFlBIF-Picture4.png)
![img](https://pic.leetcode-cn.com/1603117385-ZrhoFK-Picture5.png)
![img](https://pic.leetcode-cn.com/1603117385-xJrjxc-Picture6.png)
![img](https://pic.leetcode-cn.com/1603117385-uaYOCC-Picture7.png)
![img](https://pic.leetcode-cn.com/1603117385-KJezRV-Picture8.png)
![img](https://pic.leetcode-cn.com/1603117385-GWylSS-Picture9.png)
![img](https://pic.leetcode-cn.com/1603117385-TjmJwG-Picture10.png)
![img](https://pic.leetcode-cn.com/1603117385-dSelCu-Picture11.png)
![img](https://pic.leetcode-cn.com/1603117385-ZCwlAy-Picture12.png)
![img](https://pic.leetcode-cn.com/1603117385-oMjbol-Picture13.png)
![img](https://pic.leetcode-cn.com/1603117385-fRWVBK-Picture14.png)

#### 复杂度分析

- 时间复杂度 O(N) ： NN 为二叉树的节点数，层序遍历需要访问所有节点，最差情况下需要访问 N + 1 个 null ，总体复杂度为 O(2N + 1) = O(N) 。
- 空间复杂度 O(N) ： 最差情况下，队列 queue 同时存储 $\frac{N + 1}{2}$ 个节点（或 N+1 个 null ），使用 O(N) ；列表 res 使用 O(N) 。

#### 反序列化 Deserialize ：

基于本文开始推出的 node , node.left , node.right 在序列化列表中的位置关系，可实现反序列化。

利用队列按层构建二叉树，借助一个指针 i 指向节点 node 的左、右子节点，每构建一个 node 的左、右子节点，指针 i 就向右移动 11 位。

1. 算法流程：
   特例处理： 若 data 为空，直接返回 null ；
2. 初始化： 序列化列表 vals （先去掉首尾中括号，再用逗号隔开），指针 i = 1 ，根节点 root （值为 vals[0] ），队列 queue（包含 root ）；
3. 按层构建： 当 queue 为空时跳出；
	1. 节点出队，记为 node ；
	2. 构建 node 的左子节点：node.left 的值为 vals[i] ，并将 node.left 入队；
	3. 执行 i += 1 ；
	4. 构建 node 的右子节点：node.left 的值为 vals[i] ，并将 node.left 入队；
	5. 执行 i += 1 ；
4. 返回值： 返回根节点 root 即可；

![img](https://pic.leetcode-cn.com/1603117385-uvKOBl-Picture15.png)
![img](https://pic.leetcode-cn.com/1603117385-TRsTKE-Picture16.png)
![img](https://pic.leetcode-cn.com/1603117385-gHPLUg-Picture17.png)
![img](https://pic.leetcode-cn.com/1603117385-GVUBJX-Picture18.png)
![img](https://pic.leetcode-cn.com/1603117385-ivwlHv-Picture19.png)
![img](https://pic.leetcode-cn.com/1603117385-pIPjom-Picture20.png)
![img](https://pic.leetcode-cn.com/1603117385-NyaOkB-Picture21.png)
![img](https://pic.leetcode-cn.com/1603117385-dKntBu-Picture22.png)
![img](https://pic.leetcode-cn.com/1603117385-QbdFOw-Picture23.png)
![img](https://pic.leetcode-cn.com/1603117385-vctarq-Picture24.png)
![img](https://pic.leetcode-cn.com/1603117385-uGbitX-Picture25.png)
![img](https://pic.leetcode-cn.com/1603117385-hLRmwd-Picture26.png)
![img](https://pic.leetcode-cn.com/1603117385-Hfyuyj-Picture27.png)
![img](https://pic.leetcode-cn.com/1603117385-jKTBKw-Picture28.png)
![img](https://pic.leetcode-cn.com/1603117385-uvlvyV-Picture29.png)
![img](https://pic.leetcode-cn.com/1603117385-OKajxr-Picture30.png)
![img](https://pic.leetcode-cn.com/1603117385-wkwBBw-Picture31.png)

#### 复杂度分析：

- 时间复杂度 O(N) ： N 为二叉树的节点数，按层构建二叉树需要遍历整个 vals ，其长度最大为 2N+1。
- 空间复杂度 O(N) ： 最差情况下，队列 queue 同时存储$ \frac{N + 1}{2} $节点，因此使用 O(N) 额外空间。

### 深度优先搜索

```c++
class Codec {
public:
    void rserialize(TreeNode* root, string& str) {
        if (root == nullptr) {
            str += "None,";
        } else {
            str += to_string(root->val) + ",";
            rserialize(root->left, str);
            rserialize(root->right, str);
        }
    }

    string serialize(TreeNode* root) {
        string ret;
        rserialize(root, ret);
        return ret;
    }

    TreeNode* rdeserialize(list<string>& dataArray) {
        if (dataArray.front() == "None") {
            dataArray.erase(dataArray.begin());
            return nullptr;
        }

        TreeNode* root = new TreeNode(stoi(dataArray.front()));
        dataArray.erase(dataArray.begin());
        root->left = rdeserialize(dataArray);
        root->right = rdeserialize(dataArray);
        return root;
    }

    TreeNode* deserialize(string data) {
        list<string> dataArray;
        string str;
        for (auto& ch : data) {
            if (ch == ',') {
                dataArray.push_back(str);
                str.clear();
            } else {
                str.push_back(ch);
            }
        }
        if (!str.empty()) {
            dataArray.push_back(str);
            str.clear();
        }
        return rdeserialize(dataArray);
    }
};
```

```java
public class Codec {
    public String serialize(TreeNode root) {
        return rserialize(root, "");
    }
  
    public TreeNode deserialize(String data) {
        String[] dataArray = data.split(",");
        List<String> dataList = new LinkedList<String>(Arrays.asList(dataArray));
        return rdeserialize(dataList);
    }

    public String rserialize(TreeNode root, String str) {
        if (root == null) {
            str += "None,";
        } else {
            str += str.valueOf(root.val) + ",";
            str = rserialize(root.left, str);
            str = rserialize(root.right, str);
        }
        return str;
    }
  
    public TreeNode rdeserialize(List<String> dataList) {
        if (dataList.get(0).equals("None")) {
            dataList.remove(0);
            return null;
        }
  
        TreeNode root = new TreeNode(Integer.valueOf(dataList.get(0)));
        dataList.remove(0);
        root.left = rdeserialize(dataList);
        root.right = rdeserialize(dataList);
    
        return root;
    }
}
```

#### 思路和算法

二叉树的序列化本质上是对其值进行编码，更重要的是对其结构进行编码。可以遍历树来完成上述任务。众所周知，我们一般有两个策略：广度优先搜索和深度优先搜索。

- 广度优先搜索可以按照层次的顺序从上到下遍历所有的节点
- 深度优先搜索可以从一个根开始，一直延伸到某个叶，然后回到根，到达另一个分支。根据根节点、左节点和右节点之间的相对顺序，可以进一步将深度优先搜索策略区分为：
	- 先序遍历
	- 中序遍历
	- 后序遍历

这里，我们选择先序遍历的编码方式，我们可以通过这样一个例子简单理解：

![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/1.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/2.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/3.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/4.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/5.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/6.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/7.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/8.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/9.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/10.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/11.PNG)
![img](https://assets.leetcode-cn.com/solution-static/jianzhi_37/12.PNG)

我们从根节点 1 开始，序列化字符串是 1,。然后我们跳到根节点 2 的左子树，序列化字符串变成 1,2,。现在从节点 2 开始，我们访问它的左节点 3（1,2,3,None,None,）和右节点 4

(1,2,3,None,None,4,None,None)。None,None, 是用来标记缺少左、右子节点，这就是我们在序列化期间保存树结构的方式。最后，我们回到根节点 1 并访问它的右子树，它恰好是叶节点 5。最后，序列化字符串是 1,2,3,None,None,4,None,None,5,None,None,。

即我们可以先序遍历这颗二叉树，遇到空子树的时候序列化成 None，否则继续递归序列化。那么我们如何反序列化呢？首先我们需要根据 , 把原先的序列分割开来得到先序遍历的元素列表，然后从左向右遍历这个序列：

- 如果当前的元素为 None，则当前为空树
- 否则先解析这棵树的左子树，再解析它的右子树

#### 复杂度分析

- 时间复杂度：在序列化和反序列化函数中，我们只访问每个节点一次，因此时间复杂度为 O(n)，其中 nn 是节点数，即树的大小。
- 空间复杂度：在序列化和反序列化函数中，我们递归会使用栈空间，故渐进空间复杂度为 O(n)。

### 括号表示编码 + 递归下降解码

```c++
class Codec {
public:
    string serialize(TreeNode* root) {
        if (!root) {
            return "X";
        }
        auto left = "(" + serialize(root->left) + ")";
        auto right = "(" + serialize(root->right) + ")";
        return left + to_string(root->val) + right;
    }

    inline TreeNode* parseSubtree(const string &data, int &ptr) {
        ++ptr; // 跳过左括号
        auto subtree = parse(data, ptr);
        ++ptr; // 跳过右括号
        return subtree;
    }

    inline int parseInt(const string &data, int &ptr) {
        int x = 0, sgn = 1;
        if (!isdigit(data[ptr])) {
            sgn = -1;
            ++ptr;
        }
        while (isdigit(data[ptr])) {
            x = x * 10 + data[ptr++] - '0';
        }
        return x * sgn;
    }

    TreeNode* parse(const string &data, int &ptr) {
        if (data[ptr] == 'X') {
            ++ptr;
            return nullptr;
        }
        auto cur = new TreeNode(0);
        cur->left = parseSubtree(data, ptr);
        cur->val = parseInt(data, ptr);
        cur->right = parseSubtree(data, ptr);
        return cur;
    }

    TreeNode* deserialize(string data) {
        int ptr = 0;
        return parse(data, ptr);
    }
};
```

```java
public class Codec {
    public String serialize(TreeNode root) {
        if (root == null) {
            return "X";
        }
        String left = "(" + serialize(root.left) + ")";
        String right = "(" + serialize(root.right) + ")";
        return left + root.val + right;
    }

    public TreeNode deserialize(String data) {
        int[] ptr = {0};
        return parse(data, ptr);
    }

    public TreeNode parse(String data, int[] ptr) {
        if (data.charAt(ptr[0]) == 'X') {
            ++ptr[0];
            return null;
        }
        TreeNode cur = new TreeNode(0);
        cur.left = parseSubtree(data, ptr);
        cur.val = parseInt(data, ptr);
        cur.right = parseSubtree(data, ptr);
        return cur;
    }

    public TreeNode parseSubtree(String data, int[] ptr) {
        ++ptr[0]; // 跳过左括号
        TreeNode subtree = parse(data, ptr);
        ++ptr[0]; // 跳过右括号
        return subtree;
    }

    public int parseInt(String data, int[] ptr) {
        int x = 0, sgn = 1;
        if (!Character.isDigit(data.charAt(ptr[0]))) {
            sgn = -1;
            ++ptr[0];
        }
        while (Character.isDigit(data.charAt(ptr[0]))) {
            x = x * 10 + data.charAt(ptr[0]++) - '0';
        }
        return x * sgn;
    }
}
```

#### 思路和算法

我们也可以这样表示一颗二叉树：

- 如果当前的树为空，则表示为 X
- 如果当前的树不为空，则表示为 (<LEFT_SUB_TREE>)CUR_NUM(RIGHT_SUB_TREE)，其中：
	- <LEFT_SUB_TREE> 是左子树序列化之后的结果
	- <RIGHT_SUB_TREE> 是右子树序列化之后的结果
	- CUR_NUM 是当前节点的值

根据这样的定义，我们很好写出序列化的过程，后序遍历这颗二叉树即可，那如何反序列化呢？根据定义，我们可以推导出这样的巴科斯范式（BNF）：

$$
T -> (T) num (T) | X
$$

它的意义是：用 T 代表一棵树序列化之后的结果，| 表示 T 的构成为 (T) num (T) 或者 X，| 左边是对 T 的递归定义，右边规定了递归终止的边界条件。

因为：

- T 的定义中，序列中的第一个字符要么是 X，要么是 (，所以这个定义是不含左递归的
- 当我们开始解析一个字符串的时候，如果开头是 X，我们就知道这一定是解析一个「空树」的结构，如果开头是 (，我们就知道需要解析 (T) num (T) 的结构，因此这里两种开头和两种解析方法一一对应，可以确定这是一个无二义性的文法
- 我们只需要通过开头的第一个字母是 X 还是 ( 来判断使用哪一种解析方法

所以这个文法是 LL(1) 型文法，如果你不知道什么是 LL(1) 型文法也没有关系，你只需要知道它定义了一种递归的方法来反序列化，也保证了这个方法的正确性——我们可以设计一个递归函数：

- 这个递归函数传入两个参数，带解析的字符串和当前当解析的位置 ptr，ptr 之前的位置是已经解析的，ptr 和 ptr 后面的字符串是待解析的
- 如果当前位置为 X 说明解析到了一棵空树，直接返回
- 否则当前位置一定是 (，对括号内部按照 (T) num (T) 的模式解析

#### 复杂度分析

- 时间复杂度：序列化时做了一次遍历，渐进时间复杂度为 O(n)。反序列化时，在解析字符串的时候 ptr 指针对字符串做了一次顺序遍历，字符串长度为 O(n)，故这里的渐进时间复杂度为 O(n)。
- 空间复杂度：考虑递归使用的栈空间的大小，这里栈空间的使用和递归深度有关，递归深度又和二叉树的深度有关，在最差情况下，二叉树退化成一条链，故这里的渐进空间复杂度为 O(n)。

