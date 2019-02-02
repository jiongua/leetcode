

[TOC]

#### ARRAY_EASY_BEFORE200

##### 1.    Two Sum

Easy

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```



**我的解法**

**1：暴力解法**

思路：

> 遍历数组，设当前元素值是n[i]，则从后续元素中查找 target - n[i]，如果在n[x]找到，结果是[i, x]；如果没有找到，则从下标i+1开始重复。

复杂度：O(N^2)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if (nums.empty() || nums.size() == 1) return {};
        vector<int> res;
        for (int i = 0; i < nums.size(); i++ )
        {
            for (int j = i + 1; j < nums.size(); j++ )
            {
                if ( nums[j] == target - nums[i])
                {   
                    res.push_back(i);
                    res.push_back(j);
                    return res;
                }
            }
        }
    }
};
```

精简代码后

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if (nums.empty() || nums.size() == 1) return {};
        for (int i = 0; i < nums.size(); i++ )
        {
            for (int j = i + 1; j < nums.size(); j++ )
            {
                if ( nums[j] == target - nums[i]) return {i, j};
            }
        }
        return {};
    }
};
```

Runtime: 172 ms



**2：参考网络的two-pass哈希表解法**

思路：

> 暴力解法的时间复杂度为O(n^2)，其原因在于查找target-n[i]时再次**遍历**了数组的余下元素。
>
> 而在哈希表中进行搜索、插入和删除的平均成本是O(1)，最坏情况是O(n), 如果查找target-n[i]通过哈希表，将极大的提高查找效率.
>
> 哈希表解法以空间换取时间，第一次遍历数组时以<元素值，下标>构建哈希表；第二次遍历数组时，在查找target-n[i]时通过哈希表搜索。

复杂度：O(N)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if (nums.empty() || nums.size() == 1) return {};
        unordered_multimap<int, int> hashMap;
        for (int i = 0; i < nums.size(); i++)
            hashMap.insert({nums[i], i}); 
        for (int i = 0; i < nums.size(); i++){
            auto itr = hashMap.find(target - nums[i]);
            if(itr != hashMap.cend() && itr->second != i) 
                return {i, itr->second};
        }
        return {};
    }
};
```

Runtime: 8 ms



**3：优化哈希表解法**

思路：

> 解法2进行了两次遍历，第一次遍历仅仅是为了通过数组构造哈希表，第二次才进行搜索。
>
> 优化此解法，只进行一次遍历素组，在遍历的同时进行搜索，如果搜索不到target-nums[i]，则插入哈希表，如果搜索到了就成功返回。

复杂度：O(N)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if (nums.empty() || nums.size() == 1) return {};
        unordered_multimap<int, int> hashMap;
        for (int i = 0; i < nums.size(); i++){
            auto itr = hashMap.find(target - nums[i]);
            if(itr != hashMap.cend()) 
                return {i, itr->second};
            else
                 hashMap.insert({nums[i], i}); 
        }
        return {};
    }
};
```

Runtime: 12 ms

------

##### 26.  Remove Duplicates from Sorted Array

Easy

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.

给定一个有序数组*nums*，原地删除数组的重复元素，使每个元素只出现*一次*并返回新的长度。

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

不要分配额外的空间。

**Example 1:**

```c++
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```c++
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```c++
// nums is passed in by reference. (i.e., without making a copy)
int len = removeDuplicates(nums);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```



**我的解法**

思路：

> 1. 使用两个索引one, two，one代表没有重复的元素，下标0 到one的所有元素都不是重复的；two是为了遍历数组元素。初始时分别指向第一个元素和第二个元素；
>
> 2. 判断两个索引指向的元素是否相等，如果相等则向前移动第二索引直到两个索引指向不相等的两个元素，此时：
>
>    1）向前移动one
>
>    2）v[one] = v[two]
>
>    3） 继续向前移动two
>
> 3. 重复第二步，直到第二个索引到达数组末尾
>
> 4. 返回值是one+1

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.empty()) return 0;
        int i = 0, j = 0;
        while(++j < nums.size()){
            if(nums[j] != nums[i]) 
               nums[++i] = nums[j];  
        }
        return i+1;
    }
};
```

Runtime: 30ms



**网络的其他解法**

思路：

> 根据题目描述，最终的结果只是返回新长度，并不关心数组被如何更改，很直白的想法就是
>
> > 新长度 = 数组的长度 - 重复元素个数
>
> 只要我们找到重复的元素个数，就可以得到题目的解，例如长度为10的数组：
>
> ```
> [0,0,1,1,1,2,2,3,3,4]
> ```
>
> 如果以人的视角观察，可以看出有2个0，3个1，2个2，2个3，重复的元素总数是1+2+1+1=5
>
> 新长度就是 = 10 - 5 = 5。问题的关键在于**如何通过编程找出重复的元素个数**
>
> 
>
> 使用一个变量`count`记录重复的元素个数，从下标1开始遍历数组
>
> 1）如果当前元素v[i]与前一个v[i-1]相同，说明元素重复，更新`count`
>
> 2）如果当前元素v[i]与前一个v[i-1]不同，则更改下标i-count的元素值为当前元素。
>
> > ​     随着i的递增，count个数的递增，下标1 ~ i-count的所有元素是非重复的

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int count = 0, int n = nums.size();
        for(int i = 1; i < n; i++){
            if(nums[i] == nums[i-1]) count++;
            else nums[i-count] = nums[i];
        }
        return n-count;
    }
};
```



------

##### 27.  Remove Element

Easy

Given an array *nums* and a value *val*, remove all instances of that value [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.

根据给定val值，从数组原地移除所有元素值等于val的元素，返回新长度。不能额外分配空间。

注意：该数组不一定是有序的

**Example 1:**

```
Given nums = [3,2,2,3], val = 3,

Your function should return length = 2, with the first two elements of nums being 2.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,1,2,2,3,0,4,2], val = 2,

Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.

Note that the order of those five elements can be arbitrary.

It doesn't matter what values are set beyond the returned length.
```

**Clarification:**

Confused why the returned value is an integer but your answer is an array?

Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.

Internally you can think of this:

```
// nums is passed in by reference. (i.e., without making a copy)
int len = removeElement(nums, val);

// any modification to nums in your function would be known by the caller.
// using the length returned by your function, it prints the first len elements.
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

**我的解法**

思路：

> 解法类似与题目 26. Remove Duplicates from Sorted Array
>
> 使用双索引法解决——第一索引指向"原地构建"新的数组元素（即不包含要移除的元素），第二索引用于遍历整个数组。第一索引初始下标未0，第二索引初始下标为1
>
> 遍历数组，判断给定参数值与第二索引指向的元素值是否相等：
>
> 1）如果相等，则更新第一索引为当前下标，移动第二索引直到一个元素值与参数不同，并用这个元素值替换第一索引的元素值；最后
>
> 2） 如果不同，？？？不好描述

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        if(nums.empty()) return 0;
        int i = 0, j = 1;
        while(j < nums.size()){
            if(nums[j] == val && nums[i] == val){
                j++;
            }else if(nums[i] == val){
                swap(nums[i],nums[j]);
                i++;
                j++;
            }else {
                i++;
                j++;
            }
        }
        return nums[i] == val ? i : i+1;
    }
};
```

**根据网络解法优化思路：**

思路：

> 持有两个索引 i 和 j，其中i是跑得慢的，j是跑得快的
>
> 当nums[j]等于给定值val时，通过递增 j 跳过此元素。只要当nums[j] != val，将nums[j]复制到nums[i]并同时递增i和j。重复这个过程，直到j到达数组的末尾，新的长度为i（因为nums[i]永远不会等于val）
>
> 相比我的解法，此解法的代码精简，且nums[i]都是非val值，但依然有个缺陷，如果要删除的元素很少，没有必要对很多元素执行不必要的复制操作：`nums[i] = nums[j];`

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int i = 0;
        for (int j = 0; j < nums.size(); j++){
            if( nums[j] != val){
                nums[i] = nums[j];
                i++;
            }
        }
        return i;
    }
};
```



------

##### 35.  Search Insert Position

Easy

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

给定一个已排序数组和一个目标值，如果找到目标值则返回其下标。如果未找到，则返回将目标值按顺序插入到数组后的下标。

您可以假设数组中没有重复项。

**Example 1:**

```
Input: [1,3,5,6], 5
Output: 2
```

**Example 2:**

```
Input: [1,3,5,6], 2
Output: 1
```

**Example 3:**

```
Input: [1,3,5,6], 7
Output: 4
```

**Example 4:**

```
Input: [1,3,5,6], 0
Output: 0
```



**我的解法**

思路：

> 从有序数组中查找目标值，可以通过二分查找。
>
> 因为要返回的是下标值，如果找到，则返回的下标一定是二分查找中用到的mid
>
> 如果没有找到，此时*low == high?*,如果目标值大于low（或high），则插入的下标应该是low+1
>
> 如果目标值小于low，则插入的下标应该是low
>
> 如果只有一个元素，不会进入while循环，并且适用于上述low == high情况下下标返回值的分析

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if(nums.empty()) return 0;
        int low = 0, high = nums.size() - 1;
        while(low < high){
            int mid = low + (high - low) / 2;
            if(nums[mid] == target) 
                return mid;
            else if(nums[mid] < target)
                low = mid+1;
            else 
                high = mid-1;
        }
        //此时low必定等于high
        if(target <= nums[low]) 
            return low;
        else 
            return low+1;
    }
};
```

Runtime: 4 ms

**其他解法**

思路：

> ##### 不变量：不管是否找到目标值，返回值下标的区间一定是[low, high+1] ==>  那么low <= high + 1
>
> 退出while循环，此时low > high ==> low >= high + 1,因为low <= high + 1,所以low == high + 1
>
> 那么返回值下标区间是[low, low]，即返回值就是low

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int low = 0, high = nums.size()-1;
        while (low <= high) {
            int mid = low + (high-low)/2;
			if(nums[mid] == target) return mid;
            else if (nums[mid] < target)
                low = mid+1;
            else
                high = mid-1;
        }
        //此时low > high, low >= high + 1
        //因为返回值的下标一定是区间[low, high+1]，那么low <= high + 1;
        //所以low == high + 1，所以此时返回值区间是[low, low]，也就是答案
        return low;
    }
};
```



------

##### 53.  Maximum Subarray

**easy**

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

求最大连续子序和:

给定一个整数数组，查找具有最大和的连续子数组(至少包含一个数字)并返回其和。

**Example:**

```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Follow up:**

If you have figured out the O(*n*) solution, try coding another solution using the divide and conquer approach, which is more subtle.  

如果您已经找到了O(n)解决方案，那么可以尝试使用分治方法编写另一个解决方案，这种方法更加巧妙。

[最大连续子数列和](http://conw.net/archives/9/)

**1 暴力解法**

思路：

> 因为要求取最大连续子序和，如果穷举所有的子序列，计算每个子序列的和，然后找出和最大的值即解。
>
> 穷举所有子序列：
>
> 分别以每个元素作为起点：-2,1,-3,4,-1,2,1,-5,4
>
> 假设第i（i>=0 && i <= N-1）个元素是起点，构造N-i个子序列，每个子序列的终点分别是i, i+1,i+2...,N-1.
>
> 然后计算每个子序列的和
>
> 第一个for为了枚举所有起点，第二个for为了枚举所有以i为起点的终点，第三个是为了计算起点i与终点j之间的序列和。

时间复杂度：O(N^3)

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.empty()) return 0;
        int maxSum = nums[0];
        int len = nums.size();
        //起点为i(0~len-1）
        for(int i = 0; i < len; i++)
        {
            //终点为j（i, i+1, ..., len-1）
            for(int j = i; j < len; j++)
            {
                int sum = 0;
                //计算[i...j]子序列之和
                for(int k = i; k <= j; k++)
                {
                    sum += nums[k];
                }
                if(maxSum < sum) maxSum = sum;
            }
        }
        return maxSum;
    }
};
```



**2 利用动态规划**

思路：

> dp的关键在与将原问题划分为子问题，即子问题的格式：
>
> “求每个以i结尾的序列的最大连续和，其中0<= i <= len-1”
>
> 只要我们求出每个以i结尾的子序列的连续和，然后不断更新最大值，即可求出解
>
> 状态转移方程：
>
>  *MaxSum[i] =  MaxSum[i-1] > 0 ?   MaxSum[i-1] + A[i] : A[i]*
>
> 如果以i-1结尾的最大连续和MaxSum[i-1]是负数，则以i结尾的最大连续和为A[i]
>
> 如果以i-1结尾的最大连续和是正数，则以i结尾的最大连续和为MaxSum[i-1]+A[i]

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
		if(nums.empty()) return 0;
        int res = nums[0];
        int cur = nums[0];
        for(int i = 1; i < nums.size(); i++)
        {
            cur = max(cur + nums[i], nums[i]);
            res = max(res, cur);
        }
        return res;
    }
};
```

复杂度：O(N)

思考：如果需要返回子序列？

**3 利用分治法**

思路：

> 分治法：
>
> 把复杂大问题划分成子问题，如果子问题规模还是太大，并且它还可以继续划分，那就继续划分下去。直到这些子问题的规模已经很容易解决了，那么就把所有的子问题都解决，最后把所有的子问题合并就得到复杂大问题的答案了。
> 首先，我们可以把整个数列平均分成左右两部分，答案则会在以下三种情况中：
> 1、所求数列完全包含在左半部分的数列中。
> 2、所求数列完全包含在右半部分的数列中。
> 3、所求数列刚好横跨分割点，即左右数列各占一部分。
> 前两种情况和大问题一样，只是规模小了些，如果三个子问题都能解决，那么答案就是三个结果的最大值。我们主要研究一下第三种情况如何解决：
>
> 以分割点为起点向左的最大连续数列和、以分割点为起点向右的最大连续数列和，这两个结果的和就是第三种情况的答案。因为已知起点，所以这两个结果都能在O(N)的时间复杂度能算出来。

待续...

------

##### 66.  Plus One

Easy

Given a **non-empty** array of digits representing a non-negative integer, plus one to the integer.

The digits are stored such that the most significant digit is at the head of the list, and each element in the array contain a single digit.

You may assume the integer does not contain any leading zero, except the number 0 itself.

给定非空整形数组，整个数组代表着一个非负整数，向该整数加1返回新数组

**Example 1:**

```
Input: [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
```

**Example 2:**

```
Input: [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
```

我的解法

思路：

> 此题的关键在于进位的处理，例如[9,9]的输出将是[1,0,0]
>
> 不需要将数组转换为一个整形数值，然后加1，然后重写生成新的数值，而是直接在数组上面操作
>
> 在plus 1和进位之前，需要判断当前元素是否是9

空间复杂度O(1)

时间复杂度O(N)  如果最高位进位，则移动元素时额外加O(N)

```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        bool carry = true;
        int cursor = digits.size() - 1;
        while(cursor >= 0){
            if(digits[cursor] == 9 && carry == true){
                digits[cursor] = 0;
            }else if(carry == true){
                carry = false;
                digits[cursor]++;
            }
            cursor--;
        }
        if(carry == true){
           digits.insert(digits.begin(), 1);
        }
        return digits;
    }
};
```

Runtime: 4 ms

**优化：**

> 1. 当判断不需要进位时，即可直接返回
> 2. 不用insert以至于移动元素。

```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int cursor = digits.size() - 1;
        while(cursor >= 0){ 
            if(digits[cursor] == 9){
                digits[cursor] = 0;
            }else {
                digits[cursor]++;
                return digits;
            }
            cursor--;
        }
        //走到这里，一定是最高位需要进位：
        digits[0] = 1;
        digits.push_back(0);
        return digits;
    }
};
```

------

##### 88.  Merge Sorted Array

略，leetcode测试用例有问题

------

##### 118.Pascal's Triangle

Easy

Given a non-negative integer *numRows*, generate the first *numRows* of Pascal's triangle.

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)
In Pascal's triangle, each number is the sum of the two numbers directly above it.

每个数都是它上面两个数的和

**Example:**

```
Input: 5
Output:
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```



**我的解法**

思路：

> 每个数都是它上面两个数的和，设第i行、第j列的数值为`number[i][j]`, 则可以表达为：
>
> ```
> number[i][j] = number[i-1][j-1] + number[i-1][j];
> ```
>
> 注意考虑 `number[i-1][j-1]`的边界条件



```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        if(numRows == 0) return {};
        vector<vector<int>> res{{1}};//res[0][0] == 1
        for(int row = 1; row < numRows; row++){
            vector<int> v;
            v.push_back(res[row-1][0]);
            for(int line = 1; line < row; line++){
                v.push_back(res[row-1][line-1] + res[row-1][line]);
            }
            v.push_back(res[row-1][row-1]);
            res.push_back(std::move(v));
        }
        return res;
    }
};
```

优化：

> 先根据numRows参数构造`vector<vector<int>>`，且给每个元素赋值之前，使用resize预先分配空间
>
> res[row].resize(row+1);代表第row行含有row+1个元素

```c++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> res(numRows);
        for(int row = 0; row < numRows; row++){
            res[row].resize(row+1);
            res[row][0] = res[row][row] = 1;
            for(int line = 1; line < row; line++)
            	res[row][line] = res[row-1][line-1] + res[row-1][line];
        }
        return res;
    }
};
```

------



##### 119.Pascal's Triangle II

Easy

Given a non-negative index *k* where *k* ≤ 33, return the *k*th index row of the Pascal's triangle.

Note that the row index starts from 0.



![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)
In Pascal's triangle, each number is the sum of the two numbers directly above it.

**Example:**

```
Input: 3
Output: [1,3,3,1]
```

**Follow up:**

Could you optimize your algorithm to use only *O*(*k*) extra space?

你能优化你的算法只使用额外的*O*(*k*)空间吗?



**我的解法**

思路：

> 同118题相同，只是返回第k行的数组

空间复杂度：O(1+2+..+n) O(N^2)?

```c++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<vector<int>> v(rowIndex+1);
        for(int row = 0; row <= rowIndex; row++){
            v[row].resize(row+1);
            v[row][0] = v[row][row] = 1;
            for(int line = 1; line < row; line++)
                v[row][line] = v[row-1][line-1] + v[row-1][line];
        }
        return v[rowIndex];
    }
};
```



**空间复杂度为O*(*k)的解法**

思路：

> 

待续....



------

##### 121. Best Time to Buy and Sell Stock

Easy

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

假设有一个数组，其中第i个元素是给定股票在第i天的价格。

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

如果只允许您完成最多一项交易(即(即买一股，卖一股)，设计一种算法来寻找最大利润。

Note that you cannot sell a stock before you buy one.

注意，你不能在买股票之前就把它卖掉。

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
             Not 7-1 = 6, as selling price needs to be larger than buying price.
```

**Example 2:**

```
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```



**我的解法**

思路：

> 最大利润 = 当前价格 - 前面出现的最小价格
>
> 遍历数组，每遍历一个价格，更新最小价格，并计算当前价格下卖出的收益

时间复杂度：O(N)

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int min_price = INT_MAX, max_profit = 0, cur_max_profit = 0;
        for(int i = 0; i < prices.size(); i++){
            min_price = min(min_price, prices[i]);
            cur_max_profit = prices[i] - min_price;
            max_profit = max(max_profit, cur_max_profit);
        }
        return max_profit;
    }
};
```



------

##### 122. Best Time to Buy and Sell StockII

Easy

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

设计一种寻找最大利润的算法。您可以完成任意多的交易（多次买一股，卖一股)。

**Note:** You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
             Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
```

**Example 2:**

```
Input: [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
             Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are
             engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```



**我的解法：未想出**

**正确解法** 

思路：

> 遍历数组，计算相邻两个价格之差，如果非负（此时卖出利润>=0）则卖出计入利润总和；否则（此刻卖出会亏损），继续向前遍历。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int total = 0;
        for(int i = 1; i < prices.size(); i++){
            if(prices[i] > prices[i-1]) total += prices[i] - prices[i-1];
        }
        return total;
    }
};
```



------

##### 169. Majority Element

Easy

Given an array of size *n*, find the majority element. The majority element is the element that appears **more than** `⌊ n/2 ⌋` times.

找出数组中出现了大于n / 2次的主元素。

You may assume that the array is non-empty and the majority element always exist in the array.

假设数组非空且一定有主元素

**Example 1:**

```
Input: [3,2,3]
Output: 3
```

**Example 2:**

```
Input: [2,2,1,1,1,2,2]
Output: 2
```

**1. 我的解法**

思路：

> 使用std::map，记录遍历到的每一个元素(key)的次数(value)
>
> 可以在遍历的过程中构建std::map，同时记录value最大的值对应的key，当遍历完成即可得出解
>
> 注：题目定义主元素次数大于n/2，因为题目假设肯定有主元素，则次数最多的元素肯定是主元素，不必与n/2比较

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        map<int, int> maps;
        int max_count = 0;
        int res;
        for(int elem : nums){
            maps[elem]++;
            if(maps[elem] > max_count) {
                max_count = maps[elem];
                res = elem;
            }
        }
        return res;
    }
};
```

时间 复杂度：O(N)

Runtime: 20 ms, faster than 87.02% 



**2.其他解法：使用哈希表**

思路：

> 使用std::unordered_map，在遍历数组的过程中记录当前元素出现的总次数(`++maps[elem]`)，并且如果次数大于n/2，则提前结束
>
> 注：std::map和std::unordered_map都可以使用map[key]，而std::multimap、std::unordered_multimap不可以

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int, int> maps;
        int res;
        for(int elem : nums)
            if(++maps[elem] > nums.size()/2){
                res = elem;
                break;
            }
        return res;
    }
};
```



------

##### 189. Rotate Array

Easy

Given an array, rotate the array to the right by *k* steps, where *k* is non-negative.

给定一个数组，将数组向右旋转k个步骤，其中k是非负的。

**Example 1:**

```
Input: [1,2,3,4,5,6,7] and k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```

**Example 2:**

```
Input: [-1,-100,3,99] and k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]
```

**Note:**

- Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.

  试着想出尽可能多的解决方案，至少有3种不同的方法来解决这个问题

- Could you do it in-place with O(1) extra space?

  原地操作，不适用额外空间



**我的解法1：暴力解法**

思路：

> 进行k次循环，每一次都要将前n-1个元素向后移动一位，原先的最后一个元素填入nums[0]
>
> 考虑到k与n的关系，如果k>=n，则实际旋转的次数是k%n

时间复杂度：O(k*n)

```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        if(k >= n) k %= n; 
        for(int i = 0; i < k; i++){
            int tmp = nums[n-1];
            //前面的n-1个元素向后移动
            for(int j = n-1; j > 0; j--){
                nums[j] = nums[j-1];
            }
            nums[0] = tmp;
        }
    }
};
```

Runtime: 364 ms



**我的解法2：使用额外空间**

思路：

> 新建一个vector, 首先存放最后k个元素，再存放前面n-k元素

时间复杂度O(N)

空间复杂度O(N)

```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> res;
        if(k >= n) k = k%n;
        for(int pos = n-k; pos < n; pos++){
            res.push_back(nums[pos]);
        }
        for(int pos = 0; pos < n-k; pos++){
            res.push_back(nums[pos]);
        }
        nums = std::move(res);
    }
};
```

优化：

> (i+k) % n 即原下标为i的元素的新位置

```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> res(n);
        if(k >= n) k = k%n;
        for(int i = 0; i < n; i++){
            res[(i+k)%n] = nums[i];
        }
        nums = std::move(res);
    }
};
```

