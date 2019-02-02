[TOC]



##### 21. Merge Two Sorted Lists

**Easy**

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

合并两个有序链表，并将其作为一个新列表返回。新列表应该通过将前两个列表的节点拼接在一起来生成。

**Example:**

```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```



**我的解法: undo**

**其他解法1：递归**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1 == NULL) return l2;
        if(l2 == NULL) return l1;
        if(l1->val < l2->val){
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        }else{
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```



**其他解法2：迭代**

思路：

> 创建"哨兵", 依次比较两个链表的节点，哨兵的next指向节点值较小的节点

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *p1 = l1, *p2 = l2;
        ListNode guard(-1);
        ListNode *tail = &guard;
        while(p1 && p2){
            if(p1->val < p2->val){
                tail->next = p1;
                p1 = p1->next;
            }else{
                tail->next = p2;
                p2 = p2->next;
            }
            tail = tail->next;
        }
        if(p1 == NULL) tail->next = p2;
        if(p2 == NULL) tail->next = p1;
        return guard.next;
    }
};
```

------

##### 83. Remove Duplicates from Sorted List

**Easy**

Given a sorted linked list, delete all duplicates such that each element appear only *once*.

删除给定链表的所有重复项，使每个元素只出现一次

**Example 1:**

```
Input: 1->1->2
Output: 1->2
```

**Example 2:**

```
Input: 1->1->2->3->3
Output: 1->2->3
```

**我的解法1：**

思路：

> two-pointer方法；
>
> 需要注意最后第二个point遍历完成达到null时，要将first->next = second;

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == NULL) return NULL; 
        ListNode *first = head, *second = head->next;
        while(second){
            if(first->val == second->val){
                second = second->next;
            }else{
                first = first->next = second;
                second = second->next;
            }
        }
        first->next = second;
        return head;
    }
};
```

**其他解法：**

> 解决此题的核心想法是**从head到当前指针cur为止，所有节点都是非重复的。**
>
> 我们可以通过当前cur节点的值与cur节点之后的值进行比较来确定该节点是否是重复的。
>
> 如果它是重复的，我们将更改当前节点的下一个指针(cur->next = cur->next->next，不是直接更改cur)，以便它跳过下一个节点并直接指向下一个节点之后的下一个节点。
>
> 如果cur与cur->next的值不等，代表cur不重复，则向前移动cur，重复上诉过程
>
> 这样，当列表head到当前cur指针为止，列表中的所有节点都不包含重复的元素

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode *cur = head;
        while(cur != NULL && cur->next != NULL){
            if(cur->val == cur->next->val)
                 cur->next = cur->next->next;
            else
                cur = cur->next;   
        }
        //cur is the last node
        return head;
    }
};
```

------



##### 141.Linked List Cycle

**Easy**

Given a linked list, determine if it has a cycle in it.

给定一个链表，确定其中是否有一个循环。

To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

为了表示给定链表中的循环，我们使用一个整数pos，它表示尾节点连接到的链表中的位置(0-index)。如果pos为-1，则链表中没有循环。

**Example 1:**

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

**Example 2:**

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

**Example 3:**

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

 

**Follow up:**

Can you solve it using *O(1)* (i.e. constant) memory?

**我的解法：**

思路：

> 使用两个节点指针，初始化为head，第一个指针每次向前移动两步，第二个指针每次向前移动一步，如果第二指针或者其下一个节点为NULL，代表非循环链表；如果两个指针再都非NULL时出现相等，则代表链表有循环。

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *first = head, *second = head;
        while(second && second->next){
            second = second->next->next;
            first = first->next;
            if(second  && (first == second))
                return true;
        }
        return false;
    }
};
```

Runtime: 8 ms, faster than 97.31%



**其他解法：使用哈希表**

思路：

> 算法：
>
> 如果链表不存在循环，当遍历链表的时候，每个节点只会被访问一次；如果链表存在循环，则有的节点会被多次访问。在遍历过时检查之前是否访问过某个节点即可判断
>
> 数据结构选择：
>
> 因为不用对key进行排序，也不需要对次数进行记录，则用到记录节点是否被访问过，这里选择unordered_set更合适, 他的搜索、插入和删除具有平均常量时间复杂度。

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *cur = head;
        unordered_set<ListNode*> nodeVisit;
        while(cur){
            if(nodeVisit.count(cur) > 0) 
                return true;
            nodeVisit.insert(cur);
            cur = cur->next;
        }
        return false;
    }
};
```

Runtime: 16 ms, faster than 19.19% 

------



##### 160.Intersection of Two Linked Lists

**Easy**

Write a program to find the node at which the intersection of two singly linked lists begins.

编写一个程序来查找两个单链表的交集开始的节点。

For example, the following two linked lists:

![img](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)



begin to intersect at node c1.

**Notes:**

- If the two linked lists have no intersection at all, return `null`.
- The linked lists must retain their original structure after the function returns.
- You may assume there are no cycles anywhere in the entire linked structure.
- Your code should preferably run in O(n) time and use only O(1) memory.



**我的解法1：哈希表**

思路：

> 如果两个链表有交集，则分别遍历两个链表完成后，一定访问过相同的节点。
>
> 算法：
>
> 1）首先遍历第一个链表，使用哈希表记录每个节点地址
>
> 2）遍历第二个链表，每访问一个节点，判断当前节点地址是否存在哈希表中

时间复杂度：O(m+n)

空间复杂度：O(m) or O(n)

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == NULL || headB == NULL) return NULL;
        unordered_set<ListNode*> record;
        ListNode* p1 = headA, *p2 = headB;
        while(p1){
            record.insert(p1);
            p1 = p1->next;
        }
        while(p2){
            auto itr = record.find(p2);
            if(itr != record.end()){
                return *itr;
            }
            p2 = p2->next;
        }
        return NULL;
    }
};
```

Runtime: 40 ms, faster than 39.57% 



**其他解法：Two Ponters**

思路：

> 注意边界：从头节点开始就是交集，包括只有一个头节点的情况。所以不能通过`p1->next == p2->next`判断

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* p1 = headA, *p2 = headB;
        while(p1 && p2){
            if(p1 == p2)
                return p1;
            p1 = p1->next;
            p2 = p2->next;
            if(p1 == NULL && headB != NULL) {
                p1 = headB; 
                headB = NULL;
            }
            if(p2 == NULL && headA != NULL) {
                p2 = headA; 
                headA = NULL;
            }
        }
        return NULL;
    }
};
```

Runtime: 32 ms, faster than 96.14% 

------

#####  02. Add Two Numbers

**Medium**

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

您将得到两个非空链表，它们表示两个非负整数。数字以相反的顺序存储，每个节点都包含一个数字。将这两个数字相加并以链表的形式返回。

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

除数字本身为0外，假设这两个数字都不以0开头

**Example:**

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```



**我的解法**

思路：

> 

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dammy(-1);
        ListNode* tail = &dammy;
        int carry = 0;
        while(l1 || l2){
            tail->next = l1 ? l1 : l2;
            int sum = carry + (l1 ? l1->val : 0) + (l2 ? l2->val:0);
            carry = sum >= 10 ? 1 : 0;
            tail->next->val = carry == 1 ? sum - 10 : sum;
            l1 = l1 ? l1->next : NULL;
            l2 = l2 ? l2->next : NULL;
            tail = tail->next;
        }
        //now both l1 and l2 is null  
        if(carry == 1){
            tail->next = new ListNode(1);
        }
        return dammy.next;
    }
};
```

精简代码：

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dammy(-1);
        ListNode* tail = &dammy;
        int carry = 0;
        while(l1 || l2){
            int v1 = l1 ? l1->val:0;
            int v2 = l2 ? l2->val:0;
            tail->next = l1 ? l1 : l2;
            int sum = carry + v1 + v2;
            carry = sum / 10;
            tail->next->val = sum % 10;
            l1 = l1 ? l1->next : NULL;
            l2 = l2 ? l2->next : NULL;
            tail = tail->next;
        }
        //now both l1 and l2 is null  
        if(carry == 1){
            tail->next = new ListNode(1);
        }
        return dammy.next;
    }
};
```

**其他解法：**

思路：

> 创建每个新的ListNode

```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dammy(-1);
        ListNode* tail = &dammy;
        int carry = 0;
        while(l1 || l2 || carry){
            int sum = carry + (l1 ? l1->val:0) + (l2 ? l2->val:0);
            carry = sum / 10;
            tail->next = new ListNode(sum % 10);
            tail = tail->next;
            l1 = l1 ? l1->next : NULL;
            l2 = l2 ? l2->next : NULL;
        }
        return dammy.next;
    }
};
```

------



##### 19. Remove Nth Node From End of List

**Medium**

Given a linked list, remove the *n*-th node from the end of list and return its head.

从链表的末尾删除第n个节点（反向第N个节点）并返回表头。

**Example:**

```
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

**Note:**

Given *n* will always be valid.

**Follow up:**

Could you do this in one pass?是否可以做到一次遍历？

**我的解法：**

思路：

> 从链表中删除节点，首先考虑是否能够找到被删除节点的前一个节点。因为是从反向计数，必须有节点指针达到了链表尾部才好判定要删除的节点。
>
> 本题还要考虑边界条件：当n为0、被删除的节点是头节点，为了方便处理，引入哨兵dammy。
>
> 解题步骤：
>
> 1. 设置节点指针tail，让其最后指向最后一个节点的后一个节点，初始为head。设置节点指针pre，让其指向哨兵节点；
>
> 2. 先让tail首先向前移动，直到移动N步或者tail指向NULL，如果tail非NULL，再让pre和tail同时向前移动直到tail指向NULL。此时pre就是被删除节点的前一个节点。且这样也保证pre->next->next非空
> 3. 修改pre->next指针，完成节点删除

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(n == 0) return head;
        ListNode dammy(0);
        ListNode *pre = &dammy;
        ListNode *tail = head;
        pre->next = head;
        while(n-- > 0 && tail != NULL){
            tail = tail->next;
        }
        while(tail){
            tail = tail->next;
            pre = pre->next;
        }
        pre->next = pre->next->next;
        return dammy.next;
    }
};
```

优化：

将tail初始化为&dammy，再进行n+1步，则保证n+1步未完成之前，tail都指向非空节点。

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(n == 0) return head;
        ListNode dammy(0);
        dammy.next = head;
        ListNode *pre = &dammy;
        ListNode *tail = &dammy;
        while(n-- >= 0){//n+1 step
            tail = tail->next;
        }
        while(tail){
            tail = tail->next;
            pre = pre->next;
        }
        pre->next = pre->next->next;
        return dammy.next;
    }
};
```



------

