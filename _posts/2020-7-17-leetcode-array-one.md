---
layout: post
title: "leetcode刷题记录————数组（1）"
subtitle: "Record of writing questions————array(1)"
author: "Haye"
header-img: "img/post-bg-2020-07-17.jpg"
header-mask: 0.3
tags:
  - leetcode
  - array
  - 双指针
  - dp
---

### 1051.高度检查器

学校在拍年度纪念照时，一般要求学生按照 非递减 的高度顺序排列。请你返回能让所有学生以 非递减 高度排列的最小必要移动人数。注意，当一组学生被选中时，他们之间可以以任何可能的方式重新排序，而未被选中的学生应该保持不动。

>示例：
>
>输入：heights = [1,1,4,2,1,3]
>输出：3 
>解释：
>当前数组：[1,1,4,2,1,3]
>目标数组：[1,1,1,2,3,4]
>在下标 2 处（从 0 开始计数）出现 4 vs 1 ，所以我们必须移动这名学生。
>在下标 4 处（从 0 开始计数）出现 1 vs 3 ，所以我们必须移动这名学生。
>在下标 5 处（从 0 开始计数）出现 3 vs 4 ，所以我们必须移动这名学生。

>示例 2：
>
>输入：heights = [5,1,2,3,4]
>输出：5

> 示例3：
>
> 输入：heights = [1,2,3,4,5]
> 输出：0

将数组排序一次，对比排序前后有几个位置不一样，那么最少就要移动几次。

```c++
class Solution {
public:
    int heightChecker(vector<int>& heights) {
        vector<int> temp=heights;
        sort(heights.begin(),heights.end());
        int count=0;
        for(int i=0;i<temp.size();i++)
        {
            if(temp[i]!=heights[i])
            {
                count++;
            }
        }
        return count;
    }
};
```



### 26.删除排序数组中的重复项

给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

> 示例1：
>
> 给定数组 nums = [1,1,2], 
>
> 函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。 
>
> 你不需要考虑数组中超出新长度后面的元素。

> 示例2：
>
> 给定 nums = [0,0,1,1,1,2,2,3,3,4],
>
> 函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
>
> 你不需要考虑数组中超出新长度后面的元素。
>

*其实题目没有很看懂...*自己的理解大概就是说只需要对数组去重然后返回有效长度。在原地对数据进行改动，很容易想到双指针。

因为是排序数组，所以设定一个指针在前一个指针在后，同时向着数组尾部出发，在快的那个指针遍历完数组则循环结束。慢的那个指针代表在原数组上构建去重后的数组，快的那个负责筛选出非重复的数字。

那么就需要考虑以下两种情况：

①当两个指针指向的内容相等时。那么就需要寻找下一个不相等的元素覆盖这个重复（无效）元素，则快指针继续往前走。

②当两个指针指向的内容不等时。此时我们快指针已经找到非重复（有效）元素，所以只需要将它添加入构建的数组里，即将慢指针往后移一位，然后把快指针找到的数据放进去。

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size()==0)
        {
            return 0;
        }
        int i=0;
        if(nums.size()==1)
        {
            return i+1;
        }
        
        int j=1;
        while(j<nums.size())
        {
            if(nums[i]==nums[j])
            {
                j++;
            }
            else
            {
                i++;
                nums[i]=nums[j];
                j++;
            }
        }
        
        return i+1;
    }
};
```



### 53.最大子序列和

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

> 示例：
>
> 输入: [-2,1,-3,4,-1,2,1,-5,4],
> 输出: 6
> 解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

非常明显的动态规划。

*我个人做动态规划的习惯就是：①搞清楚DP数组应该怎么构建代表什么（从后往前去构建）②琢磨动态规划方程*

这题从后往前想，那么dp[i]应该代表以nums[i]为结尾的和最大的子序列。

那么假设已经知道dp[i-1]，即知道了以nums[i-1]为结尾的和最大的子序列，那么就可以得到动态规划方程：

dp[i]=（nums[i]+dp[i-1]<nums[i]?)nums[i]:nums[i]+dp[i-1]

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> dp(nums.size());
        dp[0]=nums[0];
        for(int i=1;i<nums.size();i++)
        {
            if(dp[i-1]<0)
            {
                dp[i]=nums[i];
            }
            else
            {
                dp[i]=nums[i]+dp[i-1];
            }
        }
        int max=dp[0];
        for(int i=1;i<dp.size();i++)
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



### 121.买卖股票的最佳时机

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

> 示例1：
>
> 输入: [7,1,5,3,6,4]
> 输出: 5
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
>      注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。

> 示例2：
>
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。

因为是复健，所以都是比较简单的题目。这题也是很简单的DP。考虑：如果我要在第i天卖出，那应该在什么时候买入，这题思路就很简单了。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()==0)
        {
            return 0;
        }
        int min=prices[0];
        
        for(int i=0;i<prices.size();i++)
        {
            if(min>prices[i])
            {
                min=prices[i];
            }
            prices[i]=prices[i]-min;
        }
        int max=0;
        for(int i=0;i<prices.size();i++)
        {
            if(max<prices[i])
            {
                max=prices[i];
            }
        }
        return max;
    }
};
```



### 1.两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

> 示例：
>
> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]

联系之后的“三数之和”，我感觉这题的正统解法应该双指针，然而我还是喜欢用哈希。

以数组的值为key，index为value构建哈希表。考虑到数组有可能存在不同index有相同的值，但题目又正好只要找出一组，所以做哈希的插入的时候遇到已经存在的key当然要选择覆盖它——否则之后做条件判断遇到**[4,1,2]，target为8**和**[4,4]，target为8**这两类情况不好分辨。

条件判断就是判断，如果差值在哈希表里存在且对应的下标不为被减数下标本身（即上文的第一个例子），则说明找到。

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> hashMap;
        vector<int> ret;
        for(int i=0;i<nums.size();i++)
        {
            hashMap[nums[i]]=i;
        }
        for(int i=0;i<nums.size();i++)
        {
            if(hashMap.find(target-nums[i])!=hashMap.end())
            {
                if(i!=hashMap[target-nums[i]])
                {
                    ret.push_back(i);
                    ret.push_back(hashMap[target-nums[i]]);
                    break;
                }
                
            }
        }
        return ret;
    }
};
```



### 15.三数之和

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

> 示例：
>
> 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
>
> 满足要求的三元组集合为：
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]

*明明就多了一个数，怎么复杂度和难度都高了那么多...*

这题不是自己想出来的，这里记一下做法：

①**对数组进行排序**

②用一个i对数组进行遍历，构造target=0-nums[i]将问题转化为求在[i+1,nums.size()-1]这个下标范围的和为target的两数之和。

这里有两个小细节，一是如果nums[i]>0的话后面的循环就不用做了，因为这个数组**排序过**，所以后面只会比0更大，根本不可能找到加起来为0的。二是如果nums[i]==nums[i-1]的话，那么就可以跳过这次循环（因为排序过，所以重复的必定相邻）。

③在循环里构造一个双指针循环，进行查找两数之和。*我之前有试过哈希，但感觉去重不如双指针好用，毕竟这题并不是找到一个两数之和的情况就可以结束了，而是要找到不重复的所有情况。*

双指针一个在头，一个在尾，两指针相遇则结束。

这里就有两种情况，**一种是两个指针指向的内容相加大于target，因为这是排序过的数组，所以就要把尾指针前移，而另一种小于的情况就是要把前指针后移**。如果是刚好等于target，说明就是要找的，把它们装入准备好的数组里。

同样，也要**对头指针和尾指针中的一个做和之前一样的去重操作**——这里操作头指针比较简单。

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {

        int target=0;
        sort(nums.begin(),nums.end());
        vector<vector<int>> ret;
        vector<int> temp;
        if(nums.size()<3)
        {
            return ret;
        }
        for(int i=0;i<nums.size()-2&& nums[i]<=0;i++)
        {
            if(i>0&&nums[i]==nums[i-1])
            {
                continue;
            }
            target=0-nums[i];
            int j=i+1;
            int k=nums.size()-1;
            while(j<k)
            {
                if(j>i+1&&nums[j]==nums[j-1])
                {
                    j++;
                    continue;
                }
                if(nums[j]+nums[k]==target)
                {
                    temp.push_back(nums[i]);
                    temp.push_back(nums[j]);
                    temp.push_back(nums[k]);
                    ret.push_back(temp);
                    j++;
                    k--;
                    temp.clear();
                }
                else if(nums[j]+nums[k]<target)
                {
                    j++;
                }
                else
                {
                    k--;
                }
            }
            
        }
        return ret;
    }
    
};
```

这题对我来说真的有点难，以后有空多复习吧。



### 11.盛最多水的容器

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

> 示例：
>
> 输入：[1,8,6,2,5,4,8,3,7]
> 输出：49

*因为暂时没搞懂githubPage怎么放图片就先不放图了，反正leetcode上有原图。*这题我看的第一眼想这不是应该是动态规划么。但琢磨了一下感觉dp是个二维数组就不太考虑思路往这走了。但迭代是肯定的，面积area=(j-i)*min(height[i],height[j])，i为起始点边index，j为终点index，然后不断迭代更新area，最后得到的area一定是最大的。

那么要怎么进行迭代呢？我们用双指针做这个题的时候，肯定是两边往中间靠拢，最后面积为0，两指针相遇结束。因为往里聚合的话，距离总是在减小，而我们要求的是是最大面积，所以在移动的时候应该确保这次移动“把短高换长高”，即谁短谁向里。

```c++
class Solution {
public:
    int min(int a,int b)
    {
       
        return (a<b)?a:b;
    }
    int maxArea(vector<int>& height) {
        int i=0;
        int j=height.size()-1;
        int area=0;
        int temp;
        while(i<j)
        {
            cout<<i<<" "<<j<<endl;
            temp=(j-i)*min(height[i],height[j]);
            area=(area<temp)?temp:area;
            if(height[i]<=height[j])
            {
                i++;
            }
            else
            {
                j--;
            }
           
            
        }
        return area;
    }
};
```

然而这段代码执行了76ms:cry:也不知道是哪里没做好优化。