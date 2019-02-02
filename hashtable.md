

[TOC]

##### 136.Single Number

Easy

Given a **non-empty** array of integers, every element appears *twice* except for one. Find that single one.

给定一个非空整数数组，除一个元素外，每个元素出现两次。找到那一个单次出现的元素。

**Note:**

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

您的算法应该具有线性运行时复杂性。您可以在不使用额外内存的情况下实现它吗

**Example 1:**

```
Input: [2,2,1]
Output: 1
```

**Example 2:**

```
Input: [4,1,2,1,2]
Output: 4
```



**我的解法1：**

思路：

> 遍历数组，使用哈希表记录每一个元素出现的次数，如果多次出现，则次数累加。当数组遍历完成后，遍历哈希表，获取value为1的Key对应的元素值。
>
> NOTE:
>
> 使用std::unordered_map作为哈希表，unordered_map支持operator[]操作，其Key-value为std::pair

时间复杂度：O(N)

空间复杂度：O(N)

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        unordered_map<int, int> hash;
        for(int i : nums)
            hash[i]++;
        for(const pair<int, int> &elem : hash){
            if(elem.second == 1)
                return elem.first;
        }
        return 0;
    }
};
```

Runtime: 12 ms, faster than 60.49%



**其他解法：位操作**

思路：

> - *a*⊕0=a
> - *a*⊕*a*=0
> - *a*⊕*b*⊕*a*=(*a*⊕*a*)⊕*b*=0⊕*b*=b
> - *a*⊕*b*⊕*a*⊕c⊕d⊕c⊕d=(a⊕a)⊕(b⊕b)⊕(c⊕c)⊕d=0⊕d=d
>
> 那么result = single_elem ⊕ twice_elem = single_elem ⊕ 0 = single_elem.

时间复杂度：O(N)

空间复杂度：O(1)

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for(int i : nums)
            res ^= i;
        return res;
    }
};
```



------

##### 003.Longest Substring Without Repeating Characters

**Medium**

Given a string, find the length of the **longest substring** without repeating characters.

给定一个字符串，查找不重复字符的最长子字符串的长度。

**Example 1:**

```
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

**Example 2:**

```
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```



**参考解法：**

思路：

> 基本思想是，使用一个unordered_map将字符串中的字符存储为键，将它们的下标存储为值，并保留两个定义最大子字符串的索引，这里可以使用(start, i)代表当前最大。移动 i 遍历字符串，同时更新unordered_map。如果当前字符已经在unordered_map中，判断其下标是否在(start, i)范围内，如果是则要更新start，否则存储字符到unordered_map。每遇到一个字符都要更新maxlen。

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        unordered_map<char, int> hash;
        int start = 0, maxlen = 0;
        for(int i = 0; i < n; i++){
            auto itr = hash.find(s[i]);
            if(itr != hash.end() && itr->second >= start)
                start = itr->second+1;
            hash[s[i]] = i;
            maxlen = max(maxlen, i - start + 1);
        }
        return maxlen;
    }
};
```

------

##### 49. Group Anagrams

**Medium**

Given an array of strings, group anagrams together.

**Example:**

```
Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
Output:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**Note:**

- All inputs will be in lowercase.
- The order of your output does not matter.



**我的解法：**

思路：

> 相同字符、不同顺序组成的string如何归纳？

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<int> vec(26,0);
        unordered_map<string, vector<string>> kv;
        vector<vector<string>> res;
        for(int i = 0; i < strs.size(); i++){
            int len = strs[i].size();
            for(int j = 0; j < len; j++)
                vec[strs[i][j] - 'a']++;
            string key(len,'0');
            for(int e = 0, k = 0; k < len && e < 26; e++){
                while(vec[e] > 0){
                    key[k++] = 'a' + e;
                    vec[e]--;
                }
            }
            kv[key].push_back(strs[i]);
            vec.resize(26,0);
        }
        for(const auto &elem: kv)
            res.push_back(elem.second);
        return res;
    }
};
```

Runtime: 20 ms, faster than 97.25%

------



##### 94. Binary Tree Inorder Traversal

**Medium**

Given a binary tree, return the *inorder* traversal of its nodes' values.

给定一个二叉树，返回中序遍历的节点值。

**Example:**

```
Input: [1,null,2,3]
   1
    \
     2
    /
   3

Output: [1,3,2]
```

**Follow up:** Recursive solution is trivial, could you do it iteratively?

递归解决方案很简单，您可以迭代地完成它吗?

**我的解法1：递归**

思路：

> 二叉树的中序遍历：以左子树-根-右子树的顺序遍历二叉树

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
    void inorderTraversal(TreeNode* root, vector<int>& vec) {
        if(root != NULL){
            inorderTraversal(root->left, res);
            res.push_back(root->val);
            inorderTraversal(root->right, res);
        }        
    }
    vector<int> inorderTraversal(TreeNode* root) {
       	if(root == NULL) return vector<int>();
        vector<int> res;
        inorderTraversal(root, res);
        return res;
    }
};
```



**参考解法2：迭代**

思路：

> 二叉树中序遍历的迭代写法：
>
> 注意只有非NULL左子节点入stack
>
> ```c++
> void inorderTraversal(TreeNode* root){
>     if(root == NULL) return;
>     stack<TreeNode*> stk;
>     TreeNode *curr = root;
>     while(curr != NULL || !stk.emtpy()){
>         while(curr){
>             stk.push(curr);
>             curr = curr->left;
>         }
>         curr = stk.top();
>         visit(curr->val);
>         curr = curr->right;
>         stk.pop();
>     }
> }
> ```



**我的解法3：**

思路：

> 使用hash表记录左右子树是否遍历的标志
>
> 0：左右子树均未遍历
>
> 1：左子树已经遍历或者左子树为NULL，当节点重新为stack顶元素时，不再遍历左子树
>
> 2：左右子树均已遍历或者左右子树为NULL，代表当节点重新为stack顶元素时，不再遍历左、右子树

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if(root == NULL) return vector<int>();
        stack<TreeNode*> stk;
        unordered_map<TreeNode*, int> hashTable;
        vector<int> res;
        stk.push(root);
        hashTable[root] = 0;
        while(!stk.empty()){
            TreeNode *t = stk.top();
            if(hashTable[t] == 0) {
                if(t->left != NULL){
                    stk.push(t->left);
                }
                hashTable[t] = 1;
            }else if(hashTable[t] == 1){
                res.push_back(t->val);
                if(t->right){
                    stk.push(t->right);
                    hashTable[t] = 2;
                }else{
                    stk.pop();
                }
            }else {
                stk.pop();
            }
        }
        return res;
    }
};
```

Runtime: 0 ms, faster than 100.00%

------



#####  138. Copy List with Random Pointer

**Medium**

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

我的解法：

思路：

> 算法：
>
> 第一次遍历原始链表，拷贝所有节点，记录两个哈希表：
>
> 1）node_index：原始链表节点地址和对应的节点序号
>
> 2）copy_index_node：新拷贝的节点序号和对应的节点地址
>
> 当第一次遍历完成后，除了没有拷贝random指针，但知道了每个random指针指向的节点序号。
>
> 第二次遍历原始链表，专门处理每个非NULL的random指针，根据node_index的到random指向的节点序号，通过copy_index_node根据序号得到对应的新拷贝的节点，然后给random赋值即可。

时间复杂度：O(2N)

空间复杂度：O(2N)

```c++
/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        RandomListNode* curr = head;
        RandomListNode dammy(-1);
        RandomListNode* pNew = &dammy;
        unordered_map<RandomListNode*, int> node_index;
        unordered_map<int, RandomListNode*> copy_index_node;
        int index = 0;
        while(curr){
            node_index[curr] = index;
            pNew->next = new RandomListNode(curr->label);
            copy_index_node[index] = pNew->next;
            curr = curr->next;
            pNew = pNew->next;
            index++;
        }
        
        curr = head;
        pNew = dammy.next;
        while(curr){
            if(curr->random){
                 int random_index = node_index.find(curr->random)->second;
                 pNew->random = copy_index_node[random_index];
            }
            curr = curr->next;
            pNew = pNew->next;
        }
        return dammy.next;
    }
};
```

Runtime: 28 ms, faster than 96.74%



**其他解法：迭代**

思路：

> 发

```

```



**参考解法：递归**

思路：

> 递归解决方案背后的基本思想是像图一样考虑RandomListNode。链表的每个节点都有两个指针(图中的边).
>
> 在这种方法中，我们所做的只是遍历图并克隆它

