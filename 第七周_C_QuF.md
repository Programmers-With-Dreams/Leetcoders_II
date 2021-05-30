# 第七周_C_QuF

这周是异或周

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   7    |   7    |   1    |

[TOC]

## [993. 二叉树的堂兄弟节点](https://leetcode-cn.com/problems/cousins-in-binary-tree/)

简单

### 原题描述

在二叉树中，根节点位于深度 0 处，每个深度为 k 的节点的子节点位于深度 k+1 处。

如果二叉树的两个节点深度相同，但 父节点不同 ，则它们是一对堂兄弟节点。

我们给出了具有唯一值的二叉树的根节点 root ，以及树中两个不同节点的值 x 和 y 。

只有与值 x 和 y 对应的节点是堂兄弟节点时，才返回 true 。否则，返回 false。

### 解题思路

对两个树分别进行dfs，把叶子节点存起来，最后比较一下

### 解题代码

```cpp
	TreeNode *parent_x, *parent_y;
    int depth_x, depth_y;
    void bfs(TreeNode *root, int x, int y, int depth) {
        if(depth_x && depth_y) { return ; }
        if(root->left) {
            if(root->left->val == x) { parent_x = root; depth_x = depth + 1; }
            if(root->left->val == y) { parent_y = root; depth_y = depth + 1; }
            bfs(root->left, x, y, depth + 1);
        }
        if(root->right) {
            if(root->right->val == x) { parent_x = root; depth_x = depth + 1; }
            if(root->right->val == y) { parent_y = root; depth_y = depth + 1; }
            bfs(root->right, x, y, depth + 1);
        }
    }
    bool isCousins(TreeNode* root, int x, int y) {
        bfs(root, x, y, 0);
        if(parent_x != parent_y && depth_x == depth_y) { return true; }
        else { return false; }
    }
```

## [1442. 形成两个异或相等数组的三元组数目](https://leetcode-cn.com/problems/count-triplets-that-can-form-two-arrays-of-equal-xor/)

中等

### 原题描述

给你一个整数数组 arr 。

现需要从数组中取三个下标 i、j 和 k ，其中 (0 <= i < j <= k < arr.length) 。

a 和 b 定义如下：

a = arr[i] ^ arr[i + 1] ^ ... ^ arr[j - 1]
b = arr[j] ^ arr[j + 1] ^ ... ^ arr[k]
注意：^ 表示 按位异或 操作。

请返回能够令 a == b 成立的三元组 (i, j , k) 的数目。

### 解题思路

连续的异或就涉及到前缀和了

拿到前缀和 `preXorSum` 之后，可以发现 只要出现 `preXorSum[i] == preXorSum[j]` 那么 `[i,j]` 就都可以实现了。

可以把前缀和用 `map<int preXorSumValue, vector<int> Index>` 储存起来。

一旦 `Index` 的数量多于 1 个，那么就有成立的 `a,b` 

成立的 a,b 数量可以这么算：

```
input		1		1		1		1		1			1
Index		0		1		2		3		4			5
prexSum		1		0		1		0		1			0
map[0]		-1		-1,1	-1,1	-1,1,3	-1,1,3		-1,1,3,5
map[1]		0		0		0,2		0,2		0,2,4		0,2,4
cal: # for Index[Index] = sum(Index - map - 1)
# for Index	0		1		1		4(1+3)		4(1+3)	9(1+3+5)
result		0		1		2(1+1)	6(2+4)		10(6+4)	19(10+9)
```

### 解题代码

```cpp
    int countTriplets(vector<int>& arr) {
        int size = arr.size(), result = 0;
        vector<int> preXorSum {0, arr.at(0)};
        unordered_map<int, int> indexCount, indexSum;
        for(auto i = 1; i < size; ++i){ preXorSum.push_back(arr[i] ^ preXorSum[i]);}
        for(int i = 0; i <= size; ++i) {
            ++indexCount[preXorSum[i]];
            // 下一行是对计算步骤的数学运算
            result += (indexCount[preXorSum[i]] - 1)*i - preXorSum[i];
            indexSum[preXorSum[i]] += i + 1;
        }
        return result;
    }
```

## [1738. 找出第 K 大的异或坐标值](https://leetcode-cn.com/problems/find-kth-largest-xor-coordinate-value/)

中等

### 原题描述

给你一个二维矩阵 matrix 和一个整数 k ，矩阵大小为 m x n 由非负整数组成。

矩阵中坐标 (a, b) 的 值 可由对所有满足 0 <= i <= a < m 且 0 <= j <= b < n 的元素 matrix[i][j]（下标从 0 开始计数）执行异或运算得到。

请你找出 matrix 的所有坐标中第 k 大的值（k 的值从 1 开始计数）。

### 解题思路

举个例子就知道了

```
matrix = 	[[1 2 3 4 5]
 			 [6 7 8 9 10]]
```

其坐标是

```
(a, b) = 	[[1		1^2		1^2^3			1^2^3^4			1^2^3^4]
 			 [1^6	1^2^6^7	1^2^3^6^7^8		1^2^3^4^6^7^8^9	1^2^3^4^6^7^8^9^10]]
```

可以发现 `(i,j) = (i-1,j-1)^(i-1,j)^(i,j-1)^matrix[i][j]`，然后找到所有的再排个序就可以AC了

用优先队列代替排序可以优化

### 解题代码

```cpp
    int kthLargestValue(vector<vector<int>>& matrix, int k) {
        int sizeR = matrix.size(), sizeC = matrix.at(0).size();
        priority_queue<int, vector<int>, greater<int>> result;
        vector<vector<int>> dp(sizeR + 1, vector<int>(sizeC + 1, 0));
        for(int i = 1; i <= sizeR; ++i) {
            for(int j = 1; j <= sizeC; ++j) {
                dp[i][j] = matrix[i - 1][j - 1];
                dp[i][j] ^= dp[i - 1][j] ^ dp[i][j - 1] ^ dp[i - 1][j - 1];
                if(result.size() < k) { result.push(dp[i][j]); }
                else {
                    if(result.top() < dp[i][j]) {
                        result.pop();
                        result.push(dp[i][j]);
                    }
                }
            }
        }
        return result.top();
    }
```

## [692. 前K个高频单词](https://leetcode-cn.com/problems/top-k-frequent-words/)

中等

这题写过，就不算入内了

### 原题描述

给一非空的单词列表，返回前 *k* 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。

### 解题思路

map计数 + 优先队列找出前 k 个 + vector 整理

### 解题代码

```cpp
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> wordCount;
        for(auto word : words) { ++wordCount[word]; }
        priority_queue<pair<int, string>> kthWords;
        for(auto [word, count] : wordCount) {
            kthWords.push({-count, word});
            if(k < kthWords.size()) { kthWords.pop(); }
        }
        vector<string> result;
        while(!kthWords.empty()) { 
            result.push_back(kthWords.top().second);
            kthWords.pop(); 
        }
        reverse(result.begin(), result.end());
        return result;
    }
```

## [1035. 不相交的线](https://leetcode-cn.com/problems/uncrossed-lines/)

中等

### 原题描述

在两条独立的水平线上按给定的顺序写下 nums1 和 nums2 中的整数。

现在，可以绘制一些连接两个数字 nums1[i] 和 nums2[j] 的直线，这些直线需要同时满足满足：

- nums1[i] == nums2[j]

- 且绘制的直线不与任何其他连线（非水平线）相交。

  请注意，连线即使在端点也不能相交：每个数字只能属于一条连线。

以这种方法绘制线条，并返回可以绘制的最大连线数。

```
输入：nums1 = [1,4,2], nums2 = [1,2,4]
输出：2
解释：可以画出两条不交叉的线。
但无法画出第三条不相交的直线，因为从 nums1[1]=4 到 nums2[2]=4 的直线将与从 nums1[2]=2 到 nums2[1]=2 的直线相交。
```

### 解题思路

老办法，从暴力-》动态规划，如果暴力的化需要传入 第一条线的 index 和第二条线的 index，可以定义动态规划数组

```cpp
dp[Index1][Index2] =  max {# lines} for nums[Index1:], nums2[Index2:]
```

从第一条线的最后开始，每次向前移动一格，每次移动遍历第二条线的数据，有两种情况

- `num1[i] = num2[j]` 可以连线：在上次的基础上 `count[i][j] = count[i + 1][j + 1] + 1`
- `num1[i] != num2[j]` 无法连线：`max(count[i + 1][j], count[i][j + 1])`

### 解题代码

```cpp
    int maxUncrossedLines(vector<int>& nums1, vector<int>& nums2) {
        int size1 = nums1.size(), size2 = nums2.size();
        vector<vector<int>> count(size1 + 1, vector<int>(size2 + 2, 0));
        for(int i = size1 - 1; i >= 0; --i) {
            for(int j = size2 - 1; j >= 0; --j) {
                if(nums1[i] == nums2[j]) { count[i][j] = count[i + 1][j + 1] + 1; }
                else { count[i][j] = max(count[i + 1][j], count[i][j + 1]); }
            }
        }
        return count[0][0];
    }
```

## [810. 黑板异或游戏](https://leetcode-cn.com/problems/chalkboard-xor-game/)

困难

### 原题描述

黑板上写着一个非负整数数组 nums[i] 。Alice 和 Bob 轮流从黑板上擦掉一个数字，Alice 先手。如果擦除一个数字后，剩余的所有数字按位异或运算得出的结果等于 0 的话，当前玩家游戏失败。 (另外，如果只剩一个数字，按位异或运算得到它本身；如果无数字剩余，按位异或运算结果为 0。）

并且，轮到某个玩家时，如果当前黑板上所有数字按位异或运算结果等于 0，这个玩家获胜。

假设两个玩家每步都使用最优解，当且仅当 Alice 获胜时返回 true。

### 解题思路

举个例子

```
		1
Alice	0	(false)								
Bob
		1		2
Alice	0			(true)
Bob				0
		1		2		1
Alice	0				1	(第一次只能删1, false)
Bob				0			(删哪个都一样的)
```

找规律可以发现数组元素个数的奇偶对结果有决定性的因素

- 奇数：假如数组中有让异或和为0 的数，为了自己能赢，Alice和Bob肯定会删掉其中一个（如上面的例子）。整个数组最后就会出现Bob选两个数，Alice选1个，这种情况下Alice就必输
- 偶数：同理，Alice必赢

### 解题代码

```cpp
    bool xorGame(vector<int>& nums) {
        int xorSum = 0;
        for(auto item : nums) { xorSum ^= item; }
        if(nums.size() & 1 && xorSum) { return false; }
        else { return true; }
    }
```

## [1707. 与数组中元素的最大异或值](https://leetcode-cn.com/problems/maximum-xor-with-an-element-from-array/)

困难

### 原题描述

给你一个由非负整数组成的数组 nums 。另有一个查询数组 queries ，其中 queries[i] = [xi, mi] 。

第 i 个查询的答案是 xi 和任何 nums 数组中不超过 mi 的元素按位异或（XOR）得到的最大值。换句话说，答案是 max(nums[j] XOR xi) ，其中所有 j 均满足 nums[j] <= mi 。如果 nums 中的所有元素都大于 mi，最终答案就是 -1 。

返回一个整数数组 answer 作为查询的答案，其中 answer.length == queries.length 且 answer[i] 是第 i 个查询的答案。

### 解题思路

- 暴力

- 这题和上周的最后一题比较类似，可以转化为上周最后一题。具体点就是把queries 排序

### 解题代码

暴力（超时）

```cpp
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        sort(nums.begin(), nums.end());
        int numSize = nums.size();
        vector<int> results;
        for(auto &querie : queries) {
            int result = -1;
            auto end = upper_bound(nums.begin(), nums.end(), querie.at(1));
            for(auto iter = nums.begin(); iter != end; ++iter) {
                int temp = querie.at(0) ^ *iter;
                if(result < temp) { result = temp; }
            }
            results.push_back(result);
        }
        return results;
    }
```

前缀树

```cpp
struct TrieNode {
    TrieNode *left = nullptr;
    TrieNode *right = nullptr;
};

class Solution {
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        // 准备
        sort(nums.begin(), nums.end());
        int querieSize = queries.size(), numSize = nums.size(), querieIndex = 0;
        for(int i = 0; i < querieSize; ++i) { queries[i].push_back(i); }
        sort(queries.begin(), queries.end(),[](vector<int> &vec1, vector<int> &vec2){ return vec1[1] < vec2[1]; });
        vector<int> results(querieSize, -1);
        // do it
        for(int i = 0; querieIndex < querieSize; ) {
            if(queries[querieIndex][1] < nums[0]) { results[queries[querieIndex][2]] = -1; ++querieIndex; }
            else if(i < numSize && nums[i] <= queries[querieIndex][1]) { insert(nums[i]); ++i; }
            else { results[queries[querieIndex][2]] = find(queries[querieIndex][0]); ++querieIndex; }
        }
        return results;
    }
private:
    TrieNode *root = new TrieNode();
    void insert(int num) {
        TrieNode *curr = root;
        for(int bit = 30; bit >= 0; bit--) {
            if(num & (1 << bit)) {
                if(curr->right == nullptr) { curr->right = new TrieNode(); }
                curr = curr->right; 
            }
            else {
                if(curr->left == nullptr) { curr->left = new TrieNode(); }
                curr = curr->left;
            }
        }
    }
    int find(int num) {
        TrieNode *curr = root;
        for(int bit = 30; bit >= 0; bit--) {
            if(num & (1 << bit)) {
                if(curr->left) { num |= 1 << bit; curr = curr->left; }
                else { num &= ~(1U << bit); curr = curr->right; }
            }
            else {
                if(curr->right) { num |= 1 << bit; curr = curr->right; }
                else { num &= ~(1U << bit); curr = curr->left; }
            }
        }
        return num;
    }
};
```

