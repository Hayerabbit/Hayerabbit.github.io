---
layout: post
title: "leetcode刷题记录——数组（2）"
subtitle: "Record of writing questions——array(2)"
author: "Haye"
header-img: "img/post-bg-2020-07-16-daily.jpg"
header-mask: 0.3
tags:
  - leetcode
  - array
  - 双指针
  - 快慢指针
  - dp
  - 栈
  - 链表
  - 队列
---

### 2.两数相加

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

> 示例：
>
> 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
> 输出：7 -> 0 -> 8
> 原因：342 + 465 = 807

题目本身不难，也没啥另辟蹊径的思路，就是边界处理比较麻烦一点。

不过我写的时候遇到的最大困难还不是边界处理，而是在初始化上：之前写“ListNode *l3(0)”一直报空指针异常，但自己死活没找出来BUG烦的不行，看了以前的答案发现是要写成“ListNode *l3=new ListNode(0)”，额，好吧，盲人选手。

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *node1(0);
        ListNode *node2(0);
        ListNode *node3;
        ListNode *l3=new ListNode(0);
        node1=l1;
        node2=l2;
        int count=0;
        ListNode *end=l3;
        while(node1!=NULL||node2!=NULL||count!=0)
        {
            node3=new ListNode(0);
            if(node1!=NULL&&node2!=NULL)
            {
                node3->val=(node1->val+node2->val+count)%10;
                count=(node1->val+node2->val+count)/10;    
            }
            else if(node1!=NULL&&node2==NULL)
            {
                node3->val=(node1->val+count)%10;
                count=(node1->val+count)/10; 
            }   
            else if(node1==NULL&&node2!=NULL)
            {
                node3->val=(node2->val+count)%10;
                count=(node2->val+count)/10; 
            }
            
            else
            {
                node3->val=count;
                count=0;
            }
            
            
            end->next=node3;
            end=node3;
            if(node1!=NULL)
            {
                node1=node1->next;
            }
            if(node2!=NULL)
            {
                node2=node2->next;
            }
        }
        if(node1!=NULL)
        {
            node3=node1;
     
            end->next=node3;
            end=node3;
        }
        else if(node2!=NULL)
        {
            node3=node2;
            end->next=node3;
            end=node3;
        }
        return l3->next;
    }
};
```



------

### 206.翻转链表

反转一个单链表。

> 示例：
>
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL

这题我直接用栈逃课了，水过。

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        stack<int> ListStack;
        ListNode *temp=head;
        ListNode *list=new ListNode(0);
        ListNode *end=list;
        while(temp!=NULL)
        {
            ListStack.push(temp->val);
            temp=temp->next;
        }
        while(!ListStack.empty())
        {
            temp=new ListNode(ListStack.top());
            ListStack.pop();
            end->next=temp;
            end=temp;
        }
        return list->next;
    }
};
```



------

### 70.爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

> 示例1：
>
> 输入： 2
> 输出： 2
> 解释： 有两种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶
> 2.  2 阶

> 示例2：
> 输入： 3
> 输出： 3
> 解释： 有三种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶 + 1 阶
> 2.  1 阶 + 2 阶
> 3.  2 阶 + 1 阶

这题是非常简单的斐波那契数列，动态方程：dp[i]=dp[i-1]+dp[i-2]

```c++
class Solution {
public:
    int climbStairs(int n) {
        vector<int> dp(n+1);
        if(n==0)
        {
            return 1;
        }
        dp[1]=1;
        if(n==1)
        {
            return 1;
        }
        dp[2]=2;
        for(int i=3;i<=n;i++)
        {
            dp[i]=dp[i-1]+dp[i-2];
        }
        return dp[n];
    }
};
```



------

以上题目都是昨天凌晨写着放松的，接下来是今天的刷题。

### 31.下一个排列

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须原地修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。

>1,2,3 → 1,3,2
>3,2,1 → 1,2,3
>1,1,5 → 1,5,1

排列组合第一反应是回溯法，可惜我对回溯不熟，而且递归有可能会超时。

无头苍蝇一样地想了很久，最后还是屈辱地看了解析。

这里记录思路步骤：

>数组从后向前遍历，令当前的index为i，那么，如果nums[i] > nums[i-1]则跳出循环，把i-1位置上的数，换成i以后，比nums[i-1]大的最小的元素，然后后方剩余的元素进行排序即可。

例如：

6 1 2 4 3 0

1. 从后向前遍历，找到nums[i] > nums[i-1]的i并跳出循环：当i指向元素4时，跳出循环；
2. 令 j 从i向后找比元素2大的最小的元素：找到该元素为3；
3. 交换元素 2 和 3：6 1 2 4 3 0 变为 6 1 3 4 2 0
4. 从i+1开始，进行排序：6 1 3 4 2 0 变为 6 1 3 0 2 4

```c++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
       
     
        int len=nums.size();
        int index=0;
        for(int i=len-1;i>0;i--)
        {
            if(nums[i]>nums[i-1])
            {
                index=i;
                break;
            }
        }
        if(index==0)
        {
            sort(nums.begin()+index,nums.end());
            return;
        }
        cout<<index<<endl;
        int min=index;
        for(int i=index;i<len;i++)
        {
            if(nums[i]>nums[index-1])
            {
                if(nums[i]<nums[min])
                {
                    min=i;
                }
                
            }
        }
        int temp=nums[min];
        nums[min]=nums[index-1];
        nums[index-1]=temp;
        sort(nums.begin()+index,nums.end());
       
    }
};
```

不过这里我图省事，就没有用快排，时间效率并不高:pensive:。

------

### 225.用队列实现栈

使用队列实现栈的下列操作：

- push(x) -- 元素 x 入栈
- pop() -- 移除栈顶元素
- top() -- 获取栈顶元素
- empty() -- 返回栈是否为空

注意:

- 你只能使用队列的基本操作-- 也就是 push to back, peek/pop from front, size, 和 is empty 这些操作是合法的。
- 你所使用的语言也许不支持队列。 你可以使用 list 或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
- 你可以假设所有操作都是有效的（例如, 对一个空的栈不会调用 pop 或者 top 操作）。



队列实现栈的push不难，毕竟都是后入，不太好搞的地方在pop，这里用了双队列的做法，在pop的时候将一个队列pop出来的加入另一个队列，直到原队列只剩一个元素，而这最后一个元素正是我们要pop出去的，然后就可以把它pop出去了。实现top也是相同的思路。在做push的时候先判断哪个队列非空，就往哪个队列插入，如果都是空的就找Queue1插入。

```c++
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {

    }
    
    /** Push element x onto stack. */
    void push(int x) {
        if(Queue1.empty()&&Queue2.empty())
        {
            Queue1.push(x);
        }
        else if(!Queue1.empty())
        {
            Queue1.push(x);
        }
        else if(!Queue2.empty())
        {
            Queue2.push(x);
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        x=-1;
        if(Queue1.empty()&&Queue2.empty())
        {
            return 0;
        }
        else if(!Queue1.empty())
        {
            while(Queue1.size()>1)
            {
                int x=Queue1.front();
                Queue2.push(x);
                Queue1.pop();
            }
            x=Queue1.front();
            Queue1.pop();

        }
        else if(!Queue2.empty())
        {
            while(Queue2.size()>1)
            {
                int x=Queue2.front();
                Queue1.push(x);
                Queue2.pop();
            }
            x=Queue2.front();
            Queue2.pop();
           
        }
        return x;
    }
    
    /** Get the top element. */
    int top() {
        x=-1;
        if(Queue1.empty()&&Queue2.empty())
        {
            return -1;
        }
        else if(!Queue1.empty())
        {
            
            while(!Queue1.empty())
            {
                x=Queue1.front();
                Queue2.push(x);
                Queue1.pop();
            }
            
            

        }
        else if(!Queue2.empty())
        {
            while(!Queue2.empty())
            {
                x=Queue2.front();
                Queue1.push(x);
                Queue2.pop();
            }
           
            
           
        }
        return x;
        
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        if(Queue1.empty()&&Queue2.empty())
        {
            return true;
        }
        else
        {
            return false;
        }
    }
private:
    queue<int> Queue1;
    queue<int> Queue2;
    int x=-1;
};
```

思路应该没问题，就是可能代码上还有可优化的地方。

------

### 198.打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

> 示例1：
>
> 输入：[1,2,3,1]
> 输出：4
> 解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
>      偷窃到的最高金额 = 1 + 3 = 4 。

> 示例2：
>
> 输入：[2,7,9,3,1]
> 输出：12
> 解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
>      偷窃到的最高金额 = 2 + 9 + 1 = 12 。

《专业》《细节》:raised_hands:没人比我更懂当小偷。

这题我第一眼没想清楚——不就单数index相加的结果拿去和双数index相加的结果比较谁更大吗？《自信》《简单》

然后啊...然后就*解答错误*了:scream:看着测试的例子：2，1，1，3。才想起来这是一道动态规划。

对于第i户人家，我们可以选择偷或者不偷，什么情况选择不偷？当然是前面一户已经偷过的情况。

但我们要做的选择最优解，对于琢磨第i户人家偷不偷的小偷，在对第i户人家做偷与不偷的抉择的时候，他实际上是在比较不偷能带来的收益和偷所带来的收益的最大值。即，dp[i]为小偷造访（就是按顺序到了，但还没做出偷or不偷的抉择）到第i户人家能获取的最大值，则到第i户为止，假设第i户不偷，能带来的最大收益为dp[i-1]，假设第i户偷，所带来的收益为dp[i-2]+houses[i]。小偷要做的就是这个权衡。由此可得动态规划方程：

dp[i]=(dp[i-2]+houses[i]>dp[i-1])?dp[i-2]+houses[i]:dp[i-1]。

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size()==0)
        {
            return 0;
        }
       vector<int> dp(nums.size());
       dp[0]=nums[0];
       for(int i=1;i<nums.size();i++)
       {
           if(i==1)
           {
               dp[i]=(nums[i]>dp[i-1])?nums[i]:dp[i-1];
           }
           else
           {
               dp[i]=(dp[i-2]+nums[i]>dp[i-1])?dp[i-2]+nums[i]:dp[i-1];
           }
           
       }
       int max=dp[0];
       for(int i=1;i<nums.size();i++)
       {
           if(dp[i]>max)
           {
               max=dp[i];
           }
       }
       return max;
    }
};
```

当个小偷也是不容易啊:sweat:。

------

### 19.删除链表的倒数第n个节点

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

> 示例：
>
> 给定一个链表: 1->2->3->4->5, 和 n = 2.
>
> 当删除了倒数第二个节点后，链表变为 1->2->3->5.

说明：

给定的 n 保证是有效的。

进阶：

你能尝试使用一趟扫描实现吗？



这题是快慢指针的题，快慢指针我最烦的就是环形链表，还好这题不是，而且给的n还必定有效，边界条件也少考虑一个了蛤蛤。

它说要倒数第二个，我们可以构造两个指针slow和quick，quick总是比slow快一个节点，这样当quick遍历完链表，slow指向的就是倒数第二个。

推广到倒数第n个，quick比slow快n-1个节点即可。

但做删除不是要获取前一个节点吗？我们做一个pre指针，然后在循环体的第一句就是pre=slow，之后进行slow和quick的后移，循环的判断条件为判断到quick是最后一个节点就可以退出循环，这样得到的pre就是要删除的节点的前驱节点了。

特殊情况是[1,2,3]删除倒数第3个。此时quick在循环之前已经遍历了数组了。那么只需要判断pre->next是否为空就可以对这种情况做特别的处理。

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head==NULL)
        {
            return NULL;
        }
        ListNode *low=head;
        ListNode *quick=head;
        for(int i=0;i<n-1;i++)
        {
            quick=quick->next;
        }
        ListNode *pre=new ListNode(0);
        while(quick->next!=NULL)
        {
            pre=low;
            quick=quick->next;
            low=low->next;
        }
        if(pre->next!=NULL)
        {
            pre->next=low->next;
            return head;
        }
        else
        {
            return head->next;
        }
        
    }
};
```

