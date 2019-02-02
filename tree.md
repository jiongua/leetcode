[TOC]



##### 100.Same Tree

Easy

Given two binary trees, write a function to check if they are the same or not.

给定两个二叉树，编写一个函数来检查它们是否相同。

Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

如果两个二叉树的结构相同且节点值相同，则认为它们是相同的。

**Example 1:**

```
Input:     1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

Output: true
```

**Example 2:**

```
Input:     1         1
          /           \
         2             2

        [1,2],     [1,null,2]

Output: false
```

**Example 3:**

```
Input:     1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

Output: false
```



**我的解法**

思路：

> 二叉树的层次遍历算法流程：
>
> 1. 首先将root节点的指针插入队列
>
> 2. 循环判断队列非空
>
>    1)  提取并移除对头元素，并判断是否为NULL；
>
>    2)  如果非NULL，则将其left和right插入队尾
>
>    3）如果为NULL，则重复循环取下一个
>
>    
>
>    层次遍历二叉树，提取所有节点的值，代码如下
>
>    ```c++
>    vector<int> levelOrderBottom(TreeNode *p)
>    {
>        vector<int> res;
>        std::queue<int> que;
>        que.push(p);
>        while(!que.empty())
>        {
>            TreeNode *t = que.front();
>            que.pop();
>            if(t != NULL){
>                res.push_back(t->val);
>                que.push(t->left);
>                que.push(t->right);
>            }
>        }
>        return res;
>    }
>    ```



```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        std::queue<TreeNode*> queue1, queue2;
        queue1.push(p);
        queue2.push(q);
        while(!queue1.empty() && !queue2.empty()){
            TreeNode* t1, *t2;
            t1 = queue1.front();
            t2 = queue2.front();
            queue1.pop();
            queue2.pop();
            
            if(t1 && t2){
                if(t1->val != t2->val) 
                    return false;
                queue1.push(t1->left);
                queue1.push(t1->right);
                queue2.push(t2->left);
                queue2.push(t2->right);
            }else if(!t1 && !t2){
                continue;
            }else
                return false;
        }
        return true;
    }
};
```



**其他解法：递归**

思路：

> 利用递归的思想，如果两个二叉树相同，则其任意节点node、node的左子树、node的右子树都是相同的

写法1：

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == NULL || q == NULL) return (p == q);
        return (p->val == q->val 
                && isSameTree(p->left, q->left) 
                && isSameTree(p->right, q->right));
    }
};
```

写法2：

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == NULL && q == NULL) return true;
        if(p == NULL || q == NULL) return false;
        if(p->val == q->val)
        	 return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        return false;
    }
};
```



------

##### 101.Symmetric Tree

Easy

Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

给定一个二叉树，检查它是否是自身的镜像(即，围绕其中心对称)。

For example, this binary tree `[1,2,2,3,4,4,3]` is symmetric:

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```



But the following `[1,2,2,null,3,null,3]` is not:

```
    1
   / \
  2   2
   \   \
   3    3
```

**Note:**
Bonus points if you could solve it both recursively and iteratively.

如果你能递归地和迭代地解决这个问题，那就更好了。



**我的解法1：迭代法**

思路：

> 层次遍历的变种，将一个二叉树分为以root为中心、分别以root->left和root->right作为根节点的两个二叉树。root->left开始按照从左到右的顺序层次遍历，root->right开始以从右到左的顺序层次遍历。遍历时同步比较节点值是否相同。这样就将镜像问题转为比较两个二叉树是否相同，关键点是两个二叉树将子节点放入队列的方式：
>
> ```c++
> //以root->left为根节点的二叉树按照先push左节点、再push右节点
> left_queue.push(lt->left);
> left_queue.push(lt->right);
> //以root->right为根节点的二叉树push右节点、再push左节点
> right_queue.push(rt->right);
> right_queue.push(rt->left);
> ```

代码实现：

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        TreeNode* left_root = root->left;
        TreeNode* right_root = root->right;
        std::queue<TreeNode*> left_queue, right_queue;
        left_queue.push(left_root);
        right_queue.push(right_root);
        while(!left_queue.empty() && !right_queue.empty()){
            TreeNode* lt = left_queue.front();
            TreeNode* rt = right_queue.front();
            left_queue.pop();
            right_queue.pop();
            if(lt && rt){
                if(lt->val != rt->val) return false;
                left_queue.push(lt->left);
                left_queue.push(lt->right);
                right_queue.push(rt->right);
                right_queue.push(rt->left);
            }else if(!lt && !rt){
                continue;
            }else
                return false;
        }
        return true;
    }
};
```

Runtime: 4 ms, faster than 100.00%

**我的解法2：递归法**

思路：

> 性质：如果二叉树是镜像的，则对此二叉树以root-left-right的顺序遍历和以root-right-left的顺序遍历的结果是相同的。
>
> 注意对NULL节点的处理，如果当前节点是NULL，也要存放节点值而不能略过，节点值为NULL
>
> 对二叉树以root-left-right的顺序遍历：
>
> ```c++
> void dfs(TreeNode* pNode)
> {
>     if(pNode)
>     {
>         cout << pNode->val << '\n';
>         dfs(pNode->left);
>         dfs(pNode->right);
>     }
> }
> ```
>
> 对二叉树以root-right-left的顺序遍历：
>
> ```c++
> void dfs(TreeNode* pNode)
> {
>     if(pNode)
>     {
>         cout << pNode->val << '\n';
>         dfs(pNode->right);
>         dfs(pNode->left);
>     }
> }
> ```



```c++
class Solution {
public:
    void dfs(TreeNode* pNode, bool lr, vector<int> &res)
    {
        if(pNode){
            res.push_back(pNode->val);
            if(lr == true){
                dfs(pNode->left, lr, res);
                dfs(pNode->right, lr, res);
            }else {
                dfs(pNode->right, lr, res);
                dfs(pNode->left, lr, res);
            }
        }else
            //不能忽略
            res.push_back(NULL);
    }
    
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        vector<int> res1, res2;
        dfs(root->left, true, res1);
        dfs(root->right, false, res2);
        return res1 == res2;
    }
};
```

Runtime: 4 ms, faster than 100.00%

**递归优化**

> 递归解法中，需要遍历完成后再比较两个vector，且遇到null节点时以NULL作为节点值存放可能有问题
>
> 优化的解法将在**遍历的过程中比较**分别以root-left-right和root-right-left的顺序遍历二叉树的节点值

```c++
class Solution {
public:
    bool dfs(TreeNode* left_root, TreeNode* right_root)
    {
        if(left_root == NULL && right_root == NULL)
            return true;
        else if(left_root != NULL && right_root != NULL){
            if(left_root->val != right_root->val) return false;
            return dfs(left_root->left, right_root->right) 
                	&& dfs(left_root->right, right_root->left);
        }else 
            return false;
    }
    
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return dfs(root->left, root->right);
    }
};
```

Runtime: 4 ms, faster than 100.00%

------



##### 104.Maximum Depth of Binary Tree

Easy

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

给定一个二叉树，求其最大深度。最大深度是从根节点到最远叶子节点的最长路径上的节点数。

**Note:** A leaf is a node with no children.

**Example:**

Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

return its depth = 3.



**我的解法1：递归法**

思路：

> 要求一个二叉树的最大深度，即其左子树最大深度与右子树最大深度的较大值 + 1，1代表当前非空根节点的深度。
>
> maxdeepth(root) = max(maxdeepth(root->left), maxdeepth(root->right)) + 1;
>
> 使用递归方法，结束条件是当前节点为null，返回0

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

Runtime: 8 ms, faster than 59.69%

**我的解法2：迭代法** 

思路：

> 参考层次遍历二叉树的思想，使用一个fifo队列存放遍历过程中遇到的**非空节点**，如果某个节点的左右子节点任意一个非空，则将level++。为防止左右子树同时增加深度，每次弹出同一个层次的所有节点。

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        std::queue<TreeNode*> que;
        int deepth = 0;
        que.push(root);
        while(!que.empty()){
            deepth++;
            for(int counts = que.size(); counts > 0; counts--){
                TreeNode *pNode = que.front();
                que.pop();
				if(pNode->left)  que.push(pNode->left);
                if(pNode->right) que.push(pNode->right); 
            }    
        }
        return deepth;
    }
};
```

------

##### 107. Binary Tree Level Order Traversal II

Easy

Given a binary tree, return the *bottom-up level order* traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

给定一个二叉树，返回其节点值的**自底向上顺序**遍历。(即从左到右，从叶到根一层一层地)。

For example:
Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回自底向上的顺序遍历:

```
[
  [15,7],
  [9,20],
  [3]
]
```



**我的解法1：迭代层次遍历**

思路1：

> 层次遍历，先自顶向下，将同一层的节点保存在一个vector<int>中，所有层次的结果保存在vector<vector<int>>中，最后将这个vector反转，即得到**自底向上**的解。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> res_top2bottom;//save from top to bottom
        queue<TreeNode*> que;
        if(root == NULL) return res_top2bottom;
        que.push(root);
        while(!que.empty()){
            int counts = que.size();
            vector<int> same_level;
            while(counts--){
                TreeNode* t = que.front();
                que.pop();
                same_level.push_back(t->val);
                if(t->left) que.push(t->left);
                if(t->right) que.push(t->right);
            }
            res_top2bottom.push_back(same_level);
        }
        vector<vector<int>> res(res_top2bottom.size());
        for(int i = res_top2bottom.size() - 1, j = 0; i >= 0; i--, j++)
            res[j] = res_top2bottom[i];
        return res;
    }
};
```

Runtime: 4 ms, faster than 100.00%

**解法2：dfs递归**

思路：

> 使用dfs算法遍历(根-左-右)二叉树，关键是记录属于同一level的节点值.
>
> 使用std::map<int, vector<int>>，其中Key代表层次，value代表第level层包含的非空节点的值
>
> **Note:**
>
> 不宜使用std::vector<vector<int>>记录遍历时第level层包含的非空节点的值，因为访问v[level]可能会越界。而map[level]不会产生这样的问题

```c++
class Solution {
public:
	void dfs(TreeNode* p, int level, map<int, vector<int>> &maps)
	{
        if(p == NULL) return;
        maps[level].push_back(p->val);
        dfs(p->left, level+1,maps);
        dfs(p->right,level+1,maps);   
	}
    
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        map<int, vector<int>> maps;
        dfs(root, 0, maps);
        vector<vector<int>> res;
        for(auto ritr = maps.rbegin(); ritr != maps.rend(); ritr++)
            res.push_back(ritr->second);
        return res;
    }
};
```

Runtime: 4 ms, faster than 100.00%

------



##### 110.Balanced Binary Tree

Easy

Given a binary tree, determine if it is height-balanced.

给定一个二叉树，判断它是否高度平衡。

For this problem, a height-balanced binary tree is defined as:

高度平衡二叉树定义为:

> a binary tree in which the depth of the two subtrees of *every* node never differ by more than 1.
>
> 二叉树的每个节点的两个子树的深度相差不超过1。

**Example 1:**

Given the following tree `[3,9,20,null,null,15,7]`:

```
    3
   / \
  9  20
    /  \
   15   7
```

Return true.

**Example 2:**

Given the following tree `[1,2,2,3,3,null,null,4,4]`:

```
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

Return false.



**我的解法1：递归**

思路：

> 严格按照平衡二叉树的定义来检验树是否平衡:两棵子树的高度差不大于1，左右两棵子树都是平衡的



```c++
class Solution {
public:
    int deepth(TreeNode *p){
		return p == NULL ? 0 : max(deepth(p->left), deepth(p->right)) + 1;
    }
    bool isBalanced(TreeNode* root) {
        if(root == NULL) return true;
        return abs(deepth(root->left) - deepth(root->right)) <= 1 
        		&& isBalanced(root->left) 
        		&& isBalanced(root->right);
    }
};
```

Runtime: 8 ms, faster than 97.75%

------

##### 111.Minimum Depth of Binary Tree

Easy

Given a binary tree, find its minimum depth.

给定一个二叉树，求其最小深度。

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

最小深度是从根节点到最近的叶节点的最短路径上的节点数

**Note:** A leaf is a node with no children.

**Example:**

Given binary tree `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

return its minimum depth = 2.

**我的解法：**

思路：

> **最小深度不是左右子树最小深度的最小值+1**
>
>       3
>      / 
>     9
>   例如这个例子的最小深度是2，因为只有9是叶子节点。
>
> 最小深度应该满足：
>
> 1）如果左右子节点非空，则最小深度 = MIN（左子树最小深度，右子树最小深度） + 1；
>
> 2）如果左子节点为空，则最小深度 = 右子树最小深度 + 1；
>
> 3）如果右子节点为空，则最小深度 = 左子树最小深度 + 1；

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        if(root->left && root->right) 
            return min(minDepth(root->left), minDepth(root->right)) + 1;
        return root->left ? minDepth(root->left) + 1 : minDepth(root->right) + 1;
    }
};
```



**我的解法2：DFS**

思路：

> DFS遍历整个二叉树，访问每个叶节点，同时更新到达叶节点时的最小深度
>
> DFS将遍历整个二叉树之后才得出最小深度，时间复杂度是O(N)

```c++
class Solution {
public:
    void dfs(TreeNode* root, int &minPath, int level){
        if(!root->left && !root->right) 
            //reach leaf 
            minPath = min(minPath,level+1);
        else {
            if(root->left) dfs(root->left, minPath, level+1);
        	if(root->right) dfs(root->right, minPath, level+1);
        }
    }
    int minDepth(TreeNode* root) {
        int minPath = INT_MAX;
        if(!root) return 0;
        dfs(root, minPath, 0);
  		return minPath;
    }
};
```



**解法3：BFS**

思路：

> 自顶向下层次遍历，只要发现叶子节点，可以不用继续向下遍历余下层次，即可得出解

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        int level = 0;
        std::queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()){
            int counts = que.size();
            level++;
            //do with same level
            while(counts--){
                TreeNode *t = que.front();
            	que.pop();
                if(!t->left && !t->right){
                   return level;
                }
                if(t->left) que.push(t->left);
                if(t->right) que.push(t->right);
            }
        }
        //The code never runs here.
  		return -1;
    }
};
```



------

##### 112.Path Sum

Easy

Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

给定一个二叉树和一个sum，判断这个树是否存在一个**从根到叶**的路径，使得路径上所有节点的值相加等于给定的sum

**Example:**

Given the below binary tree and `sum = 22`,

```
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \      \
7    2      1
```

return true, as there exist a root-to-leaf path `5->4->11->2` which sum is 22.



**我的解法1：DFS递归**

思路：

> 从根节点到特定叶子节点的路径是唯一的。
>
> 使用DFS遍历二叉树，**不必记录遇到的每个节点值，而只需要记录期待叶子节点的值**

时间复杂度：O(N)

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(root == NULL) return false;
        if(root->left == NULL && root->right == NULL && root->val == sum) 
            return true;
        if(hasPathSum(root->left, sum-root->val) || hasPathSum(root->right, sum-root->val))
            return true;
        return false;
    }
};
```

代码精简：

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(root == NULL) return false;
        if(root->left == NULL && root->right == NULL) 
            return sum == root->val;
        return hasPathSum(root->left, sum-root->val) || hasPathSum(root->right, sum-root->val);
    }
};
```



**解法2：DFS迭代版**

思路：

> 先看看二叉树DFS迭代版的写法，与BFS迭代版（即层次遍历）使用queue不同，DFS迭代版使用了stack
>
> ```c++
> void dfs_iteration(TreeNode *root)
> {
>     if(root == NULL) return;
>     stack<TreeNode*> stk;
>     stk.push(root);
>     while(!stk.empty()){
>         TreeNode* t = stk.top();
>         stk.pop();
>         if(t) {
>             do_something();
>             stk.push(t->right);
>             stk.push(t->left);
>         }
>     }
> }
> ```
>
> 如果使用迭代法深度遍历二叉树，需要两个stack，分别用于
>
> 1）DFS迭代算法
>
> 2）以相应节点为根节点时，期待的值
>
> 当遇到叶子节点时，其期待的值如果等于0，则正确

**错误题解：**

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(root == NULL) return false;
        stack<TreeNode*> stk;	//always save non-node
        stk.push(root);
        int curPashSum = 0;
        while(!stk.empty()){
            TreeNode* t = stk.top();
            stk.pop();
            curPashSum += t->val;
            if(curPashSum > sum){
                curPashSum -= t->val;
                continue;
            }
            if(sum == curPashSum){
                if(t->left == NULL && t->right == NULL)
                    return true;
                curPashSum -= t->val;
                continue;
            }
            //sum < curPashSum
            if(t->right) stk.push(t->right);
            if(t->left) stk.push(t->left);
        }
        return false;
    }
};
```

原因：题目并没有限制节点值是非负的，不能使用curPashSum与sum比较

**正确题解：**

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
     	if(root == NULL) return false;
        stack<TreeNode*> stk;	//always save non-node
        stack<int> expect;
        stk.push(root);
        expect.push(sum - root->val);
        while(!stk.empty()){
        	TreeNode* t = stk.top();
            stk.pop();
            int exp = expect.top();
            expect.pop();
            if(t->right) {
                stk.push(t->right);
                expect.push(exp - t->right->val);
            }
            if(t->left){
                stk.push(t->left);
                expect.push(exp - t->left->val);
            }
            if(!t->right && !t->left && exp == 0){
                //leaf
                return true;
            }
        }
        return false;
    }
};
```



------

##### 113.Path Sum II

Medium

Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.

给定一个二叉树和一个和，找到所有从根到叶的路径，其中每个路径的和等于给定的和。

**Note:** A leaf is a node with no children.

**Example:**

Given the below binary tree and `sum = 22`,

```
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \    / \
7    2  5   1
```

Return:

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```



我的解法：递归DFS

思路：

> 相比于112，此题1）需要保存路径，2）存在多个解
>
> 在递归进行DFS遍历时，使用~~一个stack和~~一个vector保存实时路径，~~stack和vector保存的值是相同的~~，当遇到叶子节点满足条件时，将vector存放的路径放入结果；当从递归返回(回退)的时候，~~弹出stack的栈顶元素，并~~同时删除vector最后一个元素

```c++
class Solution {
public:
    void dfs(TreeNode* p, stack<int>& path, vector<int>& cur_path, int sum, vector<vector<int>>& res)
    {
        path.push(p->val);
        cur_path.push_back(p->val);
        if(p->left == NULL && p->right == NULL){
            if(sum == p->val) 
                res.push_back(cur_path);
     		return;
        }
        if(p->left)  {
            dfs(p->left, path, cur_path, sum - p->val, res);
            cur_path.pop_back();
            path.pop();
        }
        if(p->right){
            dfs(p->right, path, cur_path, sum - p->val, res);
            cur_path.pop_back();
            path.pop();
        }  
    }
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> res;
        if(root == NULL) return res;
        stack<int> path;
        vector<int> cur_path;
        dfs(root, path, cur_path, sum, res);
        return res;
    }
};
```

Runtime: 8 ms, faster than 100.00%

优化：

> 其实不需要stack保存实时路径，只需要vector

```c++
class Solution {
public:
    void dfs(TreeNode* p, stack<int>& path, vector<int>& cur_path, int sum, vector<vector<int>>& res)
    {
        cur_path.push_back(p->val);
        if(p->left == NULL && p->right == NULL){//leaf
            if(sum == p->val) 
                res.push_back(cur_path);
     		return;
        }
        //p is not leaf
        if(p->left)  {
            dfs(p->left, path, cur_path, sum - p->val, res);
            cur_path.pop_back();
        }
        if(p->right){
            dfs(p->right, path, cur_path, sum - p->val, res);
            cur_path.pop_back();
        }  
    }
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> res;
        if(root == NULL) return res;
        vector<int> cur_path;
        dfs(root, cur_path, sum, res);
        return res;
    }
};
```

------

