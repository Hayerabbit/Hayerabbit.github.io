---
layout: post
title: "leetcode刷题记录——字节跳动专题(1)"
subtitle: "Record of writing questions——ByteDance(1)"
author: "Haye"
header-img: "img/post-bg-2020-07-19.jpg"
header-mask: 0.3
tags:
  - leetcode
  - array
  - 栈
  - dp
  - 链表
---



额，摸鱼了两天，现在重新上岗。

### 42.接雨水

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

> 示例：
>
> 输入: [0,1,0,2,1,0,1,3,2,1,2,1]
> 输出: 6



这题标注难度是困难，确实很难想到...这里采用的是题解里的**单调递减栈**的做法。

什么是单调递减栈？顾名思义，就是栈里的元素做一种递减的变化（非严格的情况下支持相等）。

我们先看那些装得下雨水的地形：

[1,0,2]

[2,1,0,1,3]

[2,1,2]

毫无疑问，当你右边新加入的柱子高度比你左边的高，就说明可以构成装雨水的地形了。

所以我们的构思就是当你新入栈的元素比栈顶高，那栈顶出栈，直到栈顶比新的元素高为止，新元素入栈。

而计算面积就是在栈顶出栈的时候，那么要怎么计算呢？

对两个例子做简单分析：

在[1,0,2]中，两边1和2之间的**间隔距离**（正常相减得到的距离-1）为1，同时装水的高度为1 ，所以构成面积为1。

在[1,0,2]中，元素1和0相继入栈，遇到元素2时，首先栈顶元素0比2来的小，于是0出栈，然后栈顶1还是比2小，1出栈，2入栈。

在[3,1,2]中，两边2和2 之间间隔距离为1，同时装水的高度为（2-1），所以构成面积为1。

在[3,1,2]中，元素2和1相继入栈，遇到元素2时，首先栈顶元素1比2来的小，于是1出栈，然后栈顶3大于2，2入栈。



首先根据[3,1,2]的例子，只做了一次出栈处理，做出栈处理的栈顶为1，新元素为2，新栈顶为2，这里的**面积计算涉及到三个元素——需要做出栈处理的栈顶，做完出栈处理后的新栈顶，和要加入的新元素**。这里用height[]表示数组里柱子的高度，同时，为了方便计算距离，入栈元素应该为height[]数组的下标。i表示要做出栈处理的栈顶的index，j表示需要新入栈的元素的index，m表示i出栈后新的栈顶的元素的index。则有面积：

**area=(j-m-1)*（min(height[j],height[m])-height[i])**。

我们可以拿这个式子套一下[2,1,0,1,3]：

首先2，1，0依次入栈，然后新元素为1，比栈顶0来得大，0出栈，新元素和新栈顶之间间隔为1，而他们之间最小高度为1，与原栈顶0相减后，得到最终雨水高度为1，于是面积为1。计算完毕，1入栈，此时栈为[2,1,1]。

然后新元素3，比栈顶1来得大，1出栈，3和新栈顶1的间隔为2（注意这里的间隔是在height[]数组里算的），它们之间的最小高度为1，与原栈顶1相减，得到雨水高度为0，于是面积为0，目前3还没入栈，栈为[2,1]。

然而这还没完，3比1来的大，则栈顶1出栈，3和新栈顶2之间的间隔为3，它们之间最小高度为2，与原栈顶1相减得到雨水高度为1，于是面积为3。

然而3还是比2大，于是2再出栈，但是2出栈以后栈空了，由于没有新栈顶（或者可以吧新栈顶看为0），那么雨水的高度是不可能大于0的，所以不需要计算，3入栈，此时栈[3]，同时得到的面积为1+3=4。

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        stack<int> stack;
        int current=0;
        int sum=0;
        while(current<height.size())
        {
            while(!stack.empty()&&height[current]>height[stack.top()])
            {
                int h=height[stack.top()];
                stack.pop();
                if(stack.empty())
                {
                    break;
                }
                int distance=current-stack.top()-1;
                int min=(height[current]<height[stack.top()])?height[current]:height[stack.top()];
                sum+=distance*(min-h);
            }
            stack.push(current);
            current++;
        }
        return sum;
    }
};
```

这题真的不好想，第一次接触了单调栈的概念，虽然概念很简单但还是不太清楚用在什么地方比较合适。

------

### 221.最大正方形

在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

> 示例：
>
> 输入：
>
> 1 0 1 0 0
> 1 0 1 1 1
> 1 1 1 1 1
> 1 0 0 1 0
>
> 输出: 4

这题是动态规划，具体动态规划方程思路解释起来很麻烦，但我已经把它刻在DNA里了，看到就知道它的动态规划方程是啥样。

首先确定动态规划数组dp[i] [j]装的是以(i,j)为右下角的最大正方形的边长，matrix为二维矩阵。显而易见，当i或者j为0的时候，dp[i] [j]=matrix[i] [j]然后有：

if(matrix[i] [j]==1)

dp[i] [j]=min(dp[i-1] [j-1],min(dp[i-1] [j],dp[i] [j-1]))+1;

else

dp[i] [j]=0;

因为你如果想要构成正方形，首先你得直到两边最短能达到多长，于是有了min(dp[i-1] [j],dp[i] [j-1])，然后你还得保证它不是个空心的正方形，比如：

1 0 1 1 1

1 1 1 1 0

1 1 1 1 1

这个矩阵最大正方形面积为4而不是9，虽然以（2，3）为右下角只做min(dp[i-1] [j],dp[i] [j-1])得到的边长为2，但是由于dp[2-1] [2-1]的正方形因为dp[0] [1]为0导致的边长只有1，所以(2,3)为右下角的最大正方形实际上并不能达到如两边长所期望的那么大。

因此在做出min(dp[i-1] [j],dp[i] [j-1])后，还要把结果和dp[i-1] [j-1]碰一碰，最后得到的最小值加上本身martix[i] [j]自己的那个节点的1的边长就是以（i,j)为右下角的最大正方形的边长。

```c++
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int sideLength[1000][1000];
        int maxSide=0;
        for(int i=0;i<matrix.size();i++)
        {
            for(int j=0;j<matrix[0].size();j++)
            {
                if(i==0||j==0)
                {
                    
                    sideLength[i][j]=matrix[i][j]-'0';
                   
                }
                else
                {
                    if(matrix[i][j]-'0'==1)
                    {
                        int minSide=(sideLength[i][j-1]<sideLength[i-1][j])?sideLength[i][j-1]:sideLength[i-1][j];
                        minSide=(minSide<sideLength[i-1][j-1])?minSide:sideLength[i-1][j-1];
                        sideLength[i][j]=minSide+1;
                    }
                    else
                    {
                        sideLength[i][j]=0;
                    }
                }
                maxSide=sideLength[i][j]>maxSide?sideLength[i][j]:maxSide;
                 
            }
        }
        return maxSide*maxSide;
    }
};

```

其实还有一题最大长方形，难度为hard，回头试试，万一写出来了呢。

------

### 56.合并区间

给出一个区间的集合，请合并所有重叠的区间。

> 示例1：
>
> 输入: [[1,3],[2,6],[8,10],[15,18]]
> 输出: [[1,6],[8,10],[15,18]]
> 解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].

> 示例2：
>
> 输入: [[1,4],[4,5]]
> 输出: [[1,5]]
> 解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。

这题没啥拐弯的地方，首先把区间数组intervals排个序，然后把第一个区间加入结果数组res里。做两个指针，一个对原数组进行遍历的temp，一个对结果数组进行更新的begin。每次temp更新时，比较res[begin] [1]和intervals [temp] [0]，来决定这两个区间是合并（begin指向的区间不动或者是将begin指向的区间更新）还是将temp指向的新区间压入res数组，并begin++。

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        
        sort(intervals.begin(),intervals.end());
        int begin=0;
        int temp=1;
        vector<vector<int>> res;
        if(intervals.size()==0)
        {
            return res;
        }
        res.push_back(intervals[0]);
         
        while(temp<intervals.size())
        {
            //cout<<temp<<" "<<begin<<endl;
            if(res[begin][1]>=intervals[temp][0])
            {
                if(res[begin][1]<intervals[temp][1])
                {
                    res[begin][1]=intervals[temp][1];
                }
            }
            else
            {
                res.push_back(intervals[temp]);
                begin++;
            }
            temp++;
        }
        return res;
    }
};
```

写累了，来点轻松的题放松一下。

------

### 20.有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

> 示例1：
>
> 输入: "()"
> 输出: true

> 示例2：
>
> 输入: "()[]{}"
> 输出: true

> 示例3：
>
> 输入: "(]"
> 输出: false

> 示例4：
>
> 输入: "([)]"
> 输出: false

> 示例5：
>
> 输入: "{[]}"
> 输出: true

谁不喜欢水题呢？一遍AC成绩还很好看。

就是非常粗暴的栈的简单应用。

```c++
class Solution {
public:
    bool isValid(string s) {
        stack<int> stack;
        for(int i=0;i<s.size();i++)
        {
            if(!stack.empty())
            {
                if((stack.top()=='('&&s[i]==')')||(stack.top()=='['&&s[i]==']')||(stack.top()=='{'&&s[i]=='}'))
                {
                    stack.pop();
                }
                else
                {
                    stack.push(s[i]);
                }
            }
            else
            {
                //cout<<s[i]<<" push"<<endl;
                stack.push(s[i]);
            }
        }
        if(stack.empty())
        {
            return true;
        }
        else
        {
            return false;
        }
    }
};
```

------

### 21.合并两个有序链表

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

> 示例：
>
> 输入：1->2->4, 1->3->4
> 输出：1->1->2->3->4->4

就嗯做。

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
       
        ListNode *l3=new ListNode(0);
        ListNode *end=l3;
        ListNode *temp;
        while(l1!=nullptr&&l2!=nullptr)
        {             
            if(l1->val<=l2->val)
            {      
                temp=new ListNode(0);
                temp->val=l1->val;
                //cout<<l1->val<<"push"<<endl;
                end->next=temp;
                end=temp;
                l1=l1->next;
            }
            else
            {
                temp=new ListNode(0);
                temp->val=l2->val;
                //cout<<l2->val<<"push"<<endl;
                end->next=temp;
                end=temp;
                l2=l2->next;
            }
        }
        if(l1!=nullptr)
        {
            temp=new ListNode(0);
            temp=l1;
            end->next=temp;
            end=temp;
        }
        else if(l2!=nullptr)
        {
            temp=new ListNode(0);
            temp=l2;
            end->next=temp;
            end=temp;
        }
        return l3->next;
    }
};
```

