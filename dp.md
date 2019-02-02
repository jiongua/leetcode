[TOC]

##### 70. Climbing Stairs

**Easy**

You are climbing a stair case. It takes *n* steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given *n* will be a positive integer.

你正在爬楼梯。到达顶端需要n步。每次你可以爬1或2级台阶。你能以多少种不同的方式爬到顶端?

n为正整数。

**Example 1:**

```
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

**我的解法1：递归**

思路：

> 略

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n == 1) return 1;
        if(n == 2) return 2;
        return climbStairs(n-1) + climbStairs(n-2);
    }
};
```

**Runtime: Time Limit Exceeded**



**其他解法：动态规划**

思路：

> dp的关键是把复杂的大问题分解成子问题，它包含最优子结构性质，即它的**最优解可以由它的子问题的最优解有效地构造出来**，我们可以用动态规划来解决这个问题。
>
> 首先分析特例N==0，1，2，解分别为0，1，2
>
> 当K>2时，为了达到K点，其实只有两种方式：
>
> 1） 从K-2出发，一次走两步
>
> 2） 从K-1出发，一次走一步
>
> 设到达K点的方式数位dp[k]，则状态方程为:
>
> dp[k]  =  dp[k -1] + dp[k-2]
>
> 由此可知，任意第K( 3<=K<=N)点的解，可以通过它前面的两个(K-2,K-1)点求出，通过从第3点开始遍历，计算每个点的解，由此可以获知后续第4—N点的所有解。

**dp解法1**

利用额外空间保存每个点的解。

时间复杂度：O(N)

空间复杂度：O(N)

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n == 0) return 0;
        if(n == 1) return 1;
        if(n == 2) return 2;
        vector<int> dp(n);
        dp[0] = 1;	//point 1
        dp[1] = 2;	//point 2
        for(int k = 2; k < n; k++)
            dp[k] = dp[k-1] + dp[k-2];
        return dp[n-1];
    }
};
```

Runtime: 0 ms, faster than 100.00% 

**dp解法2**

上面的解法通过dp[i]保存第i点的解，实际上dp[i]就是**斐波纳契数**的第i个数
$$
Fib(n)  =  Fib(n-1)  + Fib(n-2)
$$
不需要保存每个点的解，只需要在遍历时记录当前点的一步、两步距离的点的解，并实时更新即可。

时间复杂度：O(N)

空间复杂度：O(1)

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n == 0) return 0;
        if(n == 1) return 1;
        if(n == 2) return 2;
        int one_step_before = 2;		//距离K点两步的点的解
        int two_step_before = 1;		//距离K点一步的点的解
        int cur_ways = 0;
        for(int k = 2; k < n; k++){
            cur_ways = one_step_before + two_step_before;
            two_step_before = one_step_before;
            one_step_before = cur_ways;
        }
        return cur_ways
    }
};
```



------

##### 198.House Robber

Easy

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

你是一个职业抢劫犯，计划沿着街道抢劫房屋。每户人家都有一定数额的现金，阻止你抢劫的唯一限制就是相邻的两户人家都有安全系统连接，如果两户相邻的人家在同一个晚上被闯入，系统会自动报警。

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

给出一个非负整数的列表，表示每所房子的钱数，确定你今晚可以抢劫的最大钱数，而不需要报警。

**Example 1:**

```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```
Input: [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```



**我的解法**

思路：

> 设第i家的钱数为Rob[i]，每到一家前判断**抢劫与否**的最大金额总数为SumRob[i]，那么状态转移方程即为：
>
> SumRob[i] = max(Sum[i-2] + Rob[i], Sum[i-1])；
>
> Sum[i-2] + Rob[i]代表抢劫这家后的最大金额总数，Sum[i-1]代表不抢劫这家的最大金额总数。

时间复杂度：O(N)

空间复杂度：O(N)

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 0) return 0;
        if(n == 1) return nums[0];
        vector<int> sumRob(n);
        sumRob[0] = nums[0];
        sumRob[1] = max(nums[1], nums[0]);
        for(int i = 2; i < n; i++)
            sumRob[i] = max(sumRob[i-2] + nums[i], sumRob[i-1]);
        return sumRob[n-1];
    }
};
```

Runtime: 0 ms, faster than 100.00% 

**优化**：不适用额外空间

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 0) return 0;
        if(n == 1) return nums[0];
        int beforeTwo = nums[0];
        int beforeOne = max(nums[0], nums[1]);
        //if maxSum set 0 and when n = 2 will always return 0;
        int maxSum = beforeOne;
        for(int i = 2; i < n; i++){
            maxSum = max(beforeTwo+nums[i], beforeOne);
            beforeTwo = beforeOne;
            beforeOne = maxSum;
        }
        return maxSum;
    }
};
```

**改进：**

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.empty()) return 0;
        int pre1 = 0, pre2 = 0;
        for(const int& num : nums){
            int tmp = pre1;
            pre1 = max(pre2+num, pre1);
            pre2 = tmp;
        }
        return pre1;
    }
};
```



------

##### 05. Longest Palindromic Substring

**Medium**

Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.

给定一个字符串s，找出s中最长的回文子字符串。您可以假设s的最大长度是1000。

**Example 1:**

```
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

**Example 2:**

```
Input: "cbbd"
Output: "bb"
```

**我的解法：**

思路：

> 遍历string的每一个字符，依次判断是否是回文、是否是最长回文

```c++
class Solution {
public:
    int isPalindrome(const string &s, int i, int j){
        int len = 0;
        while(i < j && s[i] == s[j]){
            i++;
            j--;
            len += 2;
        }
        if(i < j) return -1;
        if(i == j) return len+1;
        return len;
    }
    string longestPalindrome(string s) {
        int n = s.size();
        if(n == 0) return string();
        int maxcount = 1;
        string res(1,s[0]);
        unordered_map<char, vector<int>> hash = {{s[0], {0}}};
        for(int i = 1; i < n; i++){
            auto itr = hash.find(s[i]);
            if(itr == hash.end()){
                hash.insert({s[i], {i}});
            }else{
                
                for(int index : itr->second){
                    if(i - index + 1 <= maxcount){
                        break;
                    }
                    int palinromeCount = isPalindrome(s, index, i);
                    if(palinromeCount > maxcount){
                        maxcount = palinromeCount;
                        res = s.substr(index, i - index + 1);
                        break;
                    }
                    if(palinromeCount > 0) 
                        break;
                }
                itr->second.push_back(i);
            }
        }
        return res;
    }
};
```

Runtime: 128 ms, faster than 29.50%



**其他解法1：动态规划**

思路：

> 如果P(i，j)代表string下标i到j是否为回文，那么P(i, j)的解取决于P(i+1, j-1) && S[i] == S[j]
>
> 基础条件为：
>
> 1) 当i == j时，P(i, j) = true
>
> 2) 当i +1 == j时，P(i, j) == (S[i] == S[j])
>
> 3) 其他P(i，j) = P(i+1, j-1) && S[i] == S[j]
>
> 算法：
>
> 分别判断所有含有1，2，3....n个字符的string子序列是否是回文，记录任意下标(i,j)的bool值代表是否是回文。
>
> 即首先判断含有一个字符的所有子串：（0，0）（1，1）（2，2）...（n-1, n-1）；
>
> 在判断还有两个字符的所有子串：（0，1）（1，2）（2，3）....（n-2, n-1）；
>
> 直到判断到子串含有N个字符（0, n-1）为止
>
> 字符数从小到大的遍历，当P(i，j) 的字符数肯定是大于P(i+1, j-1)的，而后者必定先求出了值。

时间复杂度：O(N^2)

空间复杂度：O(N^2)

```c++
class Solution {
public:
    string longestPalindrome(string s){
        int n = s.size();
        vector<vector<bool>> rec(n, vector<bool>(n, false));
        string res;
        int maxLen = 0;
        for(int len = 0; len < n; len++){
            for(int i = 0; i < n - len; i++){
                int j = i + len;
                if(i == j)      
                    rec[i][j] = true;
                else if(i + 1 == j)  
                    rec[i][j] = (s[i] == s[j]);
                else 
                    rec[i][j] = (rec[i+1][j-1] && s[i] == s[j]);
                
                if(rec[i][j] && maxLen < len + 1){
                    maxLen = len + 1;
                    res = s.substr(i, len+1);
                }
            }
        }
        return res;
    }
};
```

Runtime: 124 ms, faster than 25.32%



**其他解法2：中心点算法**

待续



------

##### 62. Unique Paths

**Medium**

A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

机器人位于*m x n*网格的左上角(在下面的图中标记为“Start”)。

机器人只能在任何时间点**向下**或**向右**移动。机器人正试图到达网格的右下角(在下面的图中标记为“完成”)。

有多少种可能的唯一路径?——即每条路径是唯一的

![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)
Above is a 7 x 3 grid. How many possible unique paths are there?

**Note:** *m* and *n* will be at most 100.

**Example 1:**

```
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right
```

**Example 2:**

```
Input: m = 7, n = 3
Output: 28
```



**我的解法1：动态规划**

思路：

> 因为某一点只有两种方式到达：从左向右，从上向下。设到达点(i, j )的所有路径数是P(i，j)，那么
>
> P(i, j) = P(i - 1, j) + P(i, j - 1)
>
> 基础条件：
>
> 1） P(0, 0) = 1，即到达起始位置只有一种路径。
>
> 2）当i == 0时，只能从左向右到达，而不能从上向下，那么P(0， j)  = P(0，j-1) = P(0, j-2)... = P(0, 0) = 1；即所有第一行的点，只有通过自左向右的唯一路径方式达到。
>
> 3）当j == 0时，同理，只有通过从上向下的唯一路径方式达到。P(i,  0) = P(0, 0) = 1
>
> 其他则按照P(i, j) = P(i - 1, j) + P(i, j - 1)计算。
>
> 算法：
>
> 分别求出P(0, 0)、P(0， j)

时间复杂度：O(M*N)

空间复杂度：O(M*N)

```C++
class Solution {
public:
    int uniquePaths(int m, int n) {
        if(m == 0 || n == 0) return 0;
        vector<vector<int>> path(m, vector<int>(n, 1));
        for(int row = 1; row < m; row++){
            for(int col = 1; col < n; col++)
                path[row][col] = path[row-1][col] + path[row][col-1];
        }
        return path[m-1][n-1];
    }
};
```

Runtime: 0 ms, faster than 100.00%



------

##### 63. Unique Paths II

**Medium**

A robot is located at the top-left corner of a *m* x *n* grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

机器人位于mxn网格的左上角(在下面的图中标记为“Start”)。

机器人只能在任何时间点向下或向右移动。机器人正试图到达网格的右下角(在下面的图中标记为“完成”)。

现在考虑是否在网格中添加了一些障碍。有多少条唯一路径?

![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

An obstacle and empty space is marked as `1` and `0` respectively in the grid.

在网格中，障碍物和空位分别标记为“1”和“0”。

**Note:** *m* and *n* will be at most 100.

**Example 1:**

```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```



待做



------

##### 64. Minimum Path Sum

**Medium**

Given a *m* x *n* grid filled with non-negative numbers, find a path from top left to bottom right which *minimizes* the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

给定一个由非负数填充的m×n网格，找到一条从左上角到右下角的路径，使路径所有数字的和最小。

注意:您只能在任何时间点向下或向右移动。

**Example:**

```
Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7
Explanation: Because the path 1→3→1→1→1 minimizes the sum.
```



**我的解法1：动态规划**

思路：

> 设pathSum(i, j)代表从起点(0,0)到达(i,j)点的最小长度路径，那么
>
> pathSum(i,j) = VAL(i, j) + min(pathSum(i-1, j), pathSum(i, j-1));

时间复杂度：O(M*N)

空间复杂度：O(M*N)

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if(grid.empty()) return 0;
        int m = grid.size();
        int n = grid[0].size();
        vector<vector<int>> pathSum(m, vector<int>(n,0));
        pathSum[0][0] = grid[0][0];
        for(int col = 1; col < n; col++)
            pathSum[0][col] = grid[0][col] + pathSum[0][col-1];
        for(int row = 1; row < m; row++)
            pathSum[row][0] = grid[row][0] + pathSum[row-1][0];
        for(int row = 1; row < m; row++){
            for(int col = 1; col < n; col++)
                pathSum[row][col] = grid[row][col] + min(pathSum[row-1][col], pathSum[row][col-1]);
        }
        return pathSum[m-1][n-1];
    }
};
```

Runtime: 8 ms, faster than 53.39% 

------

##### 96. Unique Binary Search Trees

**undo**

**Medium**

Given *n*, how many structurally unique **BST's** (binary search trees) that store values 1 ... *n*?

给定n，有多少结构上唯一的BST(二叉搜索树)，存储值为1…n ?

**Example:**

```
Input: 3
Output: 5
Explanation:
Given n = 3, there are a total of 5 unique BST's:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```



------

##### 95. Unique Binary Search Trees II

**undo**

**Medium**

Given an integer *n*, generate all structurally unique **BST's** (binary search trees) that store values 1 ... *n*.

给定一个整数n，生成所有结构唯一的二叉搜索树，节点值分别为1...n

> 二叉搜索树:
>
> 它或者是一棵空树，或者是具有下列性质： 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值； 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值； 它的左、右子树也分别为[二叉排序树](https://baike.baidu.com/item/%E4%BA%8C%E5%8F%89%E6%8E%92%E5%BA%8F%E6%A0%91/10905079)。
>
> 当以左-根-右遍历二叉搜索树时，节点值从小到大有序排列。

```
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

------



##### 120.Triangle

**Medium**

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

给定一个三角形，求从上到下的最小路径和。每一步都可以移动到下面行的相邻数字。

> 移动到下行的相邻数字，加入当前（i, j）移动到下一行的(i+1, j)和(i+1, j+1)，不包括(i+1,j-1)

For example, given the following triangle

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

The minimum path sum from top to bottom is `11` (i.e., **2** + **3** + **5** + **1** = 11).

**Note:**

Bonus point if you are able to do this using only *O*(*n*) extra space, where *n* is the total number of rows in the triangle.



我的解法：动态规划

思路：

> 这是求最小路径和的问题，且每一个点（i, j）的来源路径是明确的：要么从(i-1, j-1)要么从(i-1, j)，前提是来源点有效。而状态方程是：
>
> Path(i, j) = min(Path(i-1, j-1), Path(i-1, j)) + cur(i, j);
>
> 算法：
>
> 自上而下、从左到右，设置每一个点的最小路径，直到遍历结束，需要额外O(N)保存每个点的最小路径和。

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if(triangle.empty()) return 0;
        int layers = triangle.size();
        vector<vector<int>> pathMin(layers);
        int lastMin = 0;
        for(int row = 0; row < layers; row++){
            for(int col = 0; col <= row; col++){
                lastMin = INT_MAX;
                if(row - 1 < 0){
                    lastMin = 0;
                }else {
                    if(col <= row - 1)
                        lastMin = pathMin[row-1][col];
                    if(col > 0 && col <= row)
                        lastMin = min(lastMin, pathMin[row-1][col-1]);
                }
                pathMin[row].push_back(lastMin + triangle[row][col]);
            }
        }
        lastMin = INT_MAX;
        for(int x : pathMin[layers-1]){
            if(x < lastMin)
                lastMin = x;
        }
        return lastMin;
    }
};
```



**其他解法2：**

思路：

> 设x, y是k向下的必经点，如果确定了从x,y到底部的最小路径，那么必将确定从k点到底部的最小路径和。
>
> 状态转移方程：求第k行第j列节点开始的最短路径
>
> P(k， j) = min(P(k+1, j), P(k+1, j+1)) + C(k，j)
>
> 即第k行第j列节点开始的最短路径是当前值 + 下方两个"子节点"的最短路径最小值。
>
> 算法：
>
> 自底向上，因为最后一行的所有节点值本身即是到底部最小路径，那么从倒数第二行向上遍历，求出每个节点的最短路径，直到第一行，就可以确定P(0,0)到底部的最短路径。
>
> 此题的难度在于自底向上时如何保存已经计算出的节点的最短路径
>
> 数据结构：
>
> 最多使用额外的O(N)空间，其中N为总行数，使用vector<int>, 每一个元素代表某一行第某列的到底部的最短路径值。其中初始化时path[0]...path[N-1]存放的是三角形最后一行N个节点的数值。**每遍历新的一行，动态改变其值**：
>
> `pathMin[i] = min(pathMin[i], pathMin[i+1]) + triangle[k][i];`
>
> 这里i代表某一行的第i列，当走到第一行时，算出path[0]即为最终解。

时间复杂度：1+2+3...+N，其中N为总行数

空间复杂度：总行数（也即最后一行节点数，因为每一行的节点数等于所在行数）

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if(triangle.empty()) return 0;
        int layers = triangle.size();
        vector<int> pathMin(triangle.back());  
        for(int k = layers-2; k >= 0; k--)
            for(int i = 0; i <= k; i++)
                pathMin[i] = min(pathMin[i], pathMin[i+1]) + triangle[k][i];
        return pathMin[0];
    }
};
```

