# 第二周_C_QuF

## I 本周目标

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   6    |   6    |   1    |

## II 本周刷题总结

### 第1题 [数字范围按位与](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/description/)

难度：中等

类型：[`bit-manipulation`](https://leetcode.com/tag/bit-manipulation)

#### a. 原题陈述

给你两个整数 `left` 和 `right` ，表示区间 `[left, right]` ，返回此区间内所有数字 **按位与** 的结果（包含 `left` 、`right` 端点）。

#### b. 解题思路

- 常规思路：按照题意把每个按位与一遍，当然这样会超时。
- 标准解法：根据按位与的性质，只要出现了进位( `1011` 到 `1100` )，结果进位和后面的位就都变成 0 了( `1000` )。然后由于 `+1` 也可以算是个位进位，因此就需要判断进位出现的那一位。进位出现的那一位等价于`left` 和 `right` 第一个不同的位（观察如 `111001011` 到 `111011001`，第一个不同的位是第5位，结果就是 `111000000` ）。**这样的化就可以把题目转化为找出 `left` 和 `right` 的公共前缀。**
- **其他解法**（这个十分巧妙）：按位与就是把一些1变为0，变化总是往小的变的，因此变的数字总是 `<=left` 。于是依次从低到高把 `right` 的位变为0，找到第一个 `<=left` 的，就是答案。

#### c. 解题代码

```cpp
    int rangeBitwiseAnd(int left, int right) {
        unsigned flag = INT_MIN;
        bool diff = false;
        while (flag != 0) {
            if (diff) { left &= ~flag; }
            else if ((left & flag) != (right & flag)) { diff = true; left &= ~flag; }
            flag >>= 1;
        }
        return left;
    }
```

#### d. 其他解法摘录

```cpp
    int rangeBitwiseAnd(int left, int right) {
        unsigned int flag = 1;
        while (left < right) { right &= ~flag; flag <<= 1; }
        return right;
    }
```

------

### 第2题 [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

难度：中等

类型：[`binary-search`](https://leetcode.com/tag/binary-search) | [`divide-and-conquer`](https://leetcode.com/tag/divide-and-conquer)

#### a. 原题陈述

编写一个高效的算法来搜索 `m x n` 矩阵 `matrix` 中的一个目标值 `target` 。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

#### b. 解题思路

- 观察可以发现，比较矩阵中的每个数可以把矩阵分为4个区域，左上是 `<=matrix[i][j]`的，右下是 `>=matrix[i][j]` 的。因此可以分别在行和列上进行二分搜索，一步步的缩小区域。

  >这种方法可行，但是边界条件涉及到贼多，最后会陷入无尽的dubug中

- **其他方法**：矩阵上一个数的上面一列是比自己小的，右边一行是比自己大的，下面一列是比自己大的，左边一列是比自己小的。因此可以从一个角落进行搜索。有两种方式
  - 右上角开始：一开始往左走数字减小，当 `matrix[i][j] < tager` 说明这一行走到头了，开始往下走，然后再次往左走，...，走到边界还没找到就说明查找失败
  - 左下角开始：其他的和右上角大差不差

#### c. 解题代码

边界条件问题太多，放弃了，看的答案。

#### d. 其他解法摘录

其他方法都没有这个的巧妙

```cpp
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int sizeR = matrix.size(), sizeC = matrix.at(0).size(), currR = 0, currC = sizeC - 1;
        while (currR < sizeR && 0 <= currC) {
            int temp = matrix.at(currR).at(currC);
            if (target < temp) { --currC; }
            else if (target == temp) { return true; }
            else { ++currR; }
        }
        return false;
    }
```

------

### 第3题 [剑指 Offer 05. 替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

难度：简单

#### a. 原题陈述

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

#### b. 解题思路

有 C++ 特性了就好写多了

#### c. 解题代码

```cpp
	string replaceSpace(string s) {
        string result;
        for(auto c : s) { if(c == ' ') { result += "%20"; } else { result += c; } }
        return result;
    }
```

------

### 第4题 [剑指 Offer 06. 从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

难度：简单

类型：链表

#### a. 原题陈述


输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

#### b. 解题思路

栈

#### c. 解题代码

```cpp
    vector<int> reversePrint(ListNode* head) {
        stack<int> temp;
        vector<int> result;
        while(head){ temp.push(head->val); head = head->next; }
        while(!temp.empty()){ result.push_back(temp.top()); temp.pop(); }
        return result;
    }
```

------

### 第5题 [剑指 Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

难度：中等

类型：树，递归

#### a. 原题陈述


输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

#### b. 解题思路

画个图就知道了

#### c. 解题代码

```cpp
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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int size = preorder.size();
        return buildTreeRec(preorder, inorder, 0, size, 0, size);
    }
    TreeNode * buildTreeRec(vector<int>& preorder, vector<int>& inorder, int preBegin, int preEnd, int inBegin, int inEnd) {
        if(inEnd - inBegin == 0){ return nullptr; }
        int value = preorder.at(preBegin), inRoot = inBegin;
        while(value != inorder.at(inRoot)) {
            if(inRoot < inEnd) { ++inRoot; }
            else { vaild = false; break; }
        }
        int leftLength = inRoot - inBegin, rightLength = preEnd - preBegin - leftLength - 1;
        TreeNode *root = new TreeNode(value);
        TreeNode *left = buildTreeRec(preorder, inorder, preBegin + 1, preBegin + leftLength + 1, inBegin, inBegin + leftLength);
        TreeNode *right = buildTreeRec(preorder, inorder, preBegin + leftLength + 1, preEnd, inRoot + 1, inEnd);
        root->left = left;
        root->right = right;
        return root;
    }
private:
    bool vaild = true;	// 题目输入问题了可以通过这个提高代码鲁棒性
};
```
#### d. 其他解法

leetcode官方的递归解法，还没来得急仔细看。

```cpp
	TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (!preorder.size()) { return nullptr; }
        TreeNode* root = new TreeNode(preorder[0]);
        stack<TreeNode*> stk;
        stk.push(root);
        int inorderIndex = 0;
        for (int i = 1; i < preorder.size(); ++i) {
            int preorderVal = preorder[i];
            TreeNode* node = stk.top();
            if (node->val != inorder[inorderIndex]) {
                node->left = new TreeNode(preorderVal);
                stk.push(node->left);
            }
            else {
                while (!stk.empty() && stk.top()->val == inorder[inorderIndex]) {
                    node = stk.top();
                    stk.pop();
                    ++inorderIndex;
                }
                node->right = new TreeNode(preorderVal);
                stk.push(node->right);
            }
        }
        return root;
    }
```

------

### 第6题 [剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

难度：中等

类型：栈，设计

#### a. 原题陈述

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

#### b. 解题思路

- 一个栈在删除或者添加的时候，通过另外一个栈中转一下

  > 这种效率就比较低

- 设置一个入栈一个出栈，当出栈为空的时候，就把入栈里面的元素全转移过去

#### c. 解题代码

```cpp
class CQueue {
public:
    void appendTail(int value) { data.push(value); } 
    int deleteHead() {
        int result;
        if (data.empty()) { return -1; }
        else {
            stack<int> temp;
            while(data.size() != 1) { temp.push(data.top()); data.pop(); }
            result = data.top();
            data.pop();
            while(!temp.empty()) { data.push(temp.top()); temp.pop(); }
            return result;
        }
    }
private:
    stack<int> data;
};
```
#### d. 其他解法

```cpp
class CQueue {
public:
    void appendTail(int value) { input.push(value); } 
    int deleteHead() {
        if(output.empty() && input.empty()) { return -1; }
        else {
            if(output.empty()) { while(!input.empty()) { 
                output.push(input.top()); 
                input.pop(); 
            } }
            int result = output.top();
            output.pop();
            return result;
        }
    }
private:
    stack<int> input;
    stack<int> output;
};
```

