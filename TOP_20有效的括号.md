

#### 题目

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。
有效字符串需满足：
左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

示例 1:
输入: "()"
输出: true

示例 2:
输入: "()[]{}"
输出: true
示例 3:

输入: "(]"
输出: false

示例 4:
输入: "([)]"
输出: false

示例 5:
输入: "{[]}"
输出: true

#### 我的解法

未解开

#### 其他解法

###### 栈

```python
class Solution:
    def isValid(self, s: str) -> bool:
        dic = {'{': '}',  '[': ']', '(': ')', '?': '?'}
        stack = ['?']
        for c in s:
            if c in dic: stack.append(c)
            elif dic[stack.pop()] != c: return False 
        return len(stack) == 1
```

```java
class Solution {
    private static final Map<Character,Character> map = new HashMap<Character,Character>(){{
        put('{','}'); put('[',']'); put('(',')'); put('?','?');
    }};
    public boolean isValid(String s) { 
        if(s.length() > 0 && !map.containsKey(s.charAt(0))) return false;
        LinkedList<Character> stack = new LinkedList<Character>() {{ add('?'); }};
        for(Character c : s.toCharArray()){
            if(map.containsKey(c)) stack.addLast(c);
            else if(map.get(stack.removeLast()) != c) return false;
        }
        return stack.size() == 1;
    }
}
```

**算法原理**
栈先入后出特点恰好与本题括号排序特点一致，即若遇到左括号入栈，遇到右括号时将对应栈顶左括号出栈，则遍历完所有括号后 stack 仍然为空；
建立哈希表 dic 构建左右括号对应关系：keykey 左括号，valuevalue 右括号；这样查询 22 个括号是否对应只需 O(1)时间复杂度；建立栈 stack，遍历字符串 s 并按照算法流程一一判断。

**算法流程**
如果 c 是左括号，则入栈 push；
否则通过哈希表判断括号对应关系，若 stack 栈顶出栈括号 stack.pop() 与当前遍历括号 c 不对应，则提前返回 false。

提前返回 false
​	提前返回优点： 在迭代过程中，提前发现不符合的括号并且返回，提升算法效率。
​	解决边界问题：
​		栈 stack 为空： 此时 stack.pop() 操作会报错；因此，我们采用一个取巧方法，给 stack 赋初值 ? ，并在哈希表 dic 中建立 key: '?'，value:'?'的对应关系予以配合。此时当 stack 为空且 c 为右括号时，可以正常提前返回 false；

​		字符串 s 以左括号结尾： 此情况下可以正常遍历完整个 s，但 stack 中遗留未出栈的左括号；因此，最后需返回 len(stack) == 1，以判断是否是有效的括号组合。

**复杂度分析**

- 时间复杂度 O(N)：正确的括号组合需要遍历 1遍 `s`；
- 空间复杂度 O(N)：哈希表和栈使用线性的空间大小。

###### 类似堆栈原理

```c
bool isValid(char * s){
    int geshu=0,i,count=0,key=1;  //key来防止像全是"))))"这些情况
    for( ;s[geshu]!='\0';geshu++);

    if(geshu==0)        //处理空字符串
    return true;    

    char *cp = (char *)malloc(sizeof(char)*(geshu+1));  //开辟足够空间    特殊情况,如"["而下面要用到cp[1],所以得+1
    memset(cp,0,geshu+1);
    for(i=0;i<geshu;i++)
    {
        if((s[i]=='(')||(s[i]=='{')||(s[i]=='[')){
            count++;
            cp[count]=s[i];
            key=0;
        }else if((s[i]==cp[count]+1)||(s[i]==cp[count]+2))
        {
            count--;
        }else      //这里相当于在没有出现 '('或者'['或者'{' 的情况下,出现了没有与前面相对应的 ')'或者']'或者'}'  ,那这肯定false的.  
        return false;   
    }
    free(cp);
    if( (count)||(key) ){
        return false;
    }

    return true;
}
```

从c的基础开始

```c
typedef struct Node
{
    char s;
    struct Node* next;
}node,*pnode;

typedef struct Stack
{
    pnode ptop;
    pnode pbase;
}stack,*pstack;

stack init_stack()
{
    pnode phead = (pnode)malloc(sizeof(node));
    stack S;
    S.ptop = phead;
    S.pbase = phead;
    phead->next = NULL;
    return S;
}

void destory_stack(pstack ps)
{
    pnode p = ps->ptop;
    while(ps->ptop != ps->pbase)
    {
        p = p->next;
        free(ps->ptop);
        ps->ptop = p;
    }
    free(ps->pbase);
    ps->pbase = ps->ptop = NULL;
    return;
}

void Push(pstack ps,char val)
{
    pnode pnew = (pnode)malloc(sizeof(node));
    pnew ->s = val;
    pnew ->next = ps->ptop;
    ps->ptop = pnew;
    return;
}

char getElem(pstack ps)
{
    char s = ps ->ptop ->s;
    return s;
}

void pop(pstack ps)
{
    pnode p = ps->ptop->next;
    free(ps->ptop);
    ps->ptop = p;
    return;
}

bool isEmpty(pstack ps)
{
    if(ps->ptop == ps->pbase)
        return true;
    else
        return false;
}

bool isValid(char * s){
    stack S = init_stack();
    char* p;
    p = s;
    for(;*p!='\0';p++)
    {
        if(*p == '(' || *p == '[' || *p == '{')
        {
            Push(&S,*p);
        }
        else if(*p==')')
        {
            if(isEmpty(&S))
            {
                destory_stack(&S);
                return false;
            }
            else
            {
                if(getElem(&S) == '(')
                {
                    pop(&S);
                    continue;
                }
                else
                {
                    destory_stack(&S);
                    return false;
                }
            }
        }
        else if(*p==']')
        {
            if(isEmpty(&S))
            {
                destory_stack(&S);
                return false;
            }
            else
            {
                if(getElem(&S) == '[')
                {
                    pop(&S);
                    continue;
                }
                else
                {
                    destory_stack(&S);
                    return false;
                }
            }
        }
        else if(*p=='}')
        {
            if(isEmpty(&S))
            {
                destory_stack(&S);
                return false;
            }
            else
            {
                if(getElem(&S) == '{')
                {
                    pop(&S);
                    continue;
                }
                else
                {
                    destory_stack(&S);
                    return false;
                }
            }
        }
    }
    if(isEmpty(&S))
    {
        destory_stack(&S);
        return true;
    }
    else
    {
        destory_stack(&S);
        return false;
    }
}
```

