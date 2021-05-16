# 第六周_C_QuF

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   7    |   7    |   1    |

[TOC]

## [872. 叶子相似的树](https://leetcode-cn.com/problems/leaf-similar-trees/)

- 难度：简单
- 类型：树，DFS

### 原题描述

请考虑一棵二叉树上所有的叶子，这些叶子的值按从左到右的顺序排列形成一个 叶值序列 。

如果有两棵二叉树的叶值序列是相同，那么我们就认为它们是 叶相似 的。

如果给定的两个根结点分别为 root1 和 root2 的树是叶相似的，则返回 true；否则返回 false 。

### 解题思路

把两棵树通过DFS得到叶子节点向量

然后依次判断这两个向量十分相同

### 解题代码

```cpp
    vector<int> leafDfs(TreeNode *root) {
        vector<int> leaf;
        stack<TreeNode *> stackDfs;
        stackDfs.push(root);
        while(!stackDfs.empty()) {
            TreeNode *currNode = stackDfs.top();
            stackDfs.pop();
            if(currNode->left == nullptr && currNode->right == nullptr) { leaf.push_back(currNode->val); }
            else{
                if(currNode->left ) { stackDfs.push(currNode->left ); }
                if(currNode->right) { stackDfs.push(currNode->right); }
            }
        }
        return leaf;
    }
	bool leafSimilar(TreeNode* root1, TreeNode* root2) {
        vector<int> leaf1 = leafDfs(root1), leaf2 = leafDfs(root2);
        return leaf1 == leaf2 ? true : false;
    }
```

## [1734. 解码异或后的排列](https://leetcode-cn.com/problems/decode-xored-permutation/)

难度：中等

类型：位运算

### 原题描述

给你一个整数数组 perm ，它是前 n 个正整数的排列，且 n 是个 奇数 。

它被加密成另一个长度为 n - 1 的整数数组 encoded ，满足 encoded[i] = perm[i] XOR perm[i + 1] 。比方说，如果 perm = [1,3,2] ，那么 encoded = [2,1] 。

给你 encoded 数组，请你返回原始数组 perm 。题目保证答案存在且唯一。

### 解题思路

这里第一个数出来了，后面所有数字就都出来了

利用奇数和位运算的性质就可以推导出来第一个数了

```cpp
i		:		0			1			2			3					n-1
perm[i]	:	a[0]^a[1]	a[1]^a[2]	a[2]^a[3]	a[3]^a[4]			a[n-1]^a[n]	
^perm[odd]				a[1]^a[2]				a[1]^a[2]^a[3]^a[4] a[1]^...a[n]
```

`a[0]=(^a[i])^(^prem[odd])` 其中 `^a[i]` 就是从1 异或到n

### 解题代码

```cpp
    vector<int> decode(vector<int>& encoded) {
        vector<int> tempEncode { encoded.at(0) };
        int size = encoded.size(), firstXor = 0, first = 0, allXor = 0;
        for(int i = 1; i < size; ++i) {
            if(i & 1) { firstXor ^= encoded[i]; }
            tempEncode.push_back(encoded[i] ^ tempEncode[i - 1]);
        }
        for(int i = 1; i <= size + 1; ++i) { allXor ^= i; }
        first = firstXor ^ allXor;
        vector<int> result{ first };
        for(int i = 0; i < size; ++i) {
            result.push_back(first ^ tempEncode[i]);
        }
        return result;
    }
```

## [1310. 子数组异或查询](https://leetcode-cn.com/problems/xor-queries-of-a-subarray/)

### 原题描述

有一个正整数数组 arr，现给你一个对应的查询数组 queries，其中 queries[i] = [Li, Ri]。

对于每个查询 i，请你计算从 Li 到 Ri 的 XOR 值（即 arr[Li] xor arr[Li+1] xor ... xor arr[Ri]）作为本次查询的结果。

并返回一个包含给定查询 queries 所有结果的数组。

### 解题思路

求前缀异或和

### 解题代码

```cpp
    vector<int> xorQueries(vector<int>& arr, vector<vector<int>>& queries) {
        vector<int> arrSum { arr.at(0) }, result;
        int size = arr.size();
        for(int i = 1; i < size; ++i) { 
            arrSum.push_back(arr.at(i) ^ arrSum.at(i - 1));
        }
        for(auto vec : queries) {
            if(vec.at(0)){
                result.push_back(arrSum.at(vec.at(0) - 1) ^ arrSum.at(vec.at(1)));
            }
            else { result.push_back(arrSum.at(vec.at(1))); }
        }
        return result;
    }
```

## [1269. 停在原地的方案数](https://leetcode-cn.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/)

难度：困难

类型：动态规划

### 原题描述

有一个长度为 arrLen 的数组，开始有一个指针在索引 0 处。

每一步操作中，你可以将指针向左或向右移动 1 步，或者停在原地（指针不能被移动到数组范围外）。

给你两个整数 steps 和 arrLen ，请你计算并返回：在恰好执行 steps 次操作以后，指针仍然指向索引 0 处的方案数。

由于答案可能会很大，请返回方案数 模 10^9 + 7 后的结果。

### 解题思路

动态规划

如果使用暴力DFS解法，递归的函数形式大概是 `int Rec(arrLen, step, currPos);` 返回方案数量，step是当前的步数，currPos是当前的位置。因此可以推断能用二维动态规划求解，定义 `dp[step, currPos] = # plan` 

最终要求的就是 `dp[steps, 0]`

由于一次只能走一步，$dp[step][currPost]=\sum\limits_{i=-1,0,1}[step - 1, currPos - i]$

### 解题代码

```cpp
int numWays(int steps, int arrLen) {
    int currSize = arrLen < ((steps >> 1) + 1) ? arrLen : ((steps >> 1) + 1);
    vector<vector<int>> dp(steps + 1, vector<int>(currSize, 0));
    dp[0][0] = 1;
    for(int step = 1; step <= steps; ++step) {
        for(int curr = 0; curr < currSize; ++curr) {
            dp[step][curr] = dp[step - 1][curr];
            if (curr != 0) { dp[step][curr] = (dp[step][curr] + dp[step - 1][curr - 1]) % 1000000007; }
            if (curr != currSize - 1) { dp[step][curr] = (dp[step][curr] + dp[step - 1][curr + 1]) % 1000000007; }
        }
    }
    return dp[steps][0];
}
```

## [12. 整数转罗马数字](https://leetcode-cn.com/problems/integer-to-roman/) [13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

难度：简单

类型：字符串

### 原题描述

罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。

字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

- 给你一个整数，将其转为罗马数字。
- 给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。 

### 解题思路

暴力求解

### 解题代码

```cpp
    string intToRoman(int num) {
        vector<int> bits(4, 0);
        for(int i = 3; i >= 0; --i) { 
            bits[i] = num % 10; 
            num /= 10; 
        }
        string str(bits[0], 'M');
        if(bits[1] == 4) { str += "CD"; }
        else if(bits[1] == 9) { str += "CM"; }
        else if(bits[1] >= 5) { str += "D"; str.insert(str.end(), bits[1] - 5, 'C'); }
        else { str.insert(str.end(), bits[1], 'C'); }
        
        if(bits[2] == 4) { str += "XL"; }
        else if(bits[2] == 9) { str += "XC"; }
        else if(bits[2] >= 5) { str += "L"; str.insert(str.end(), bits[2] - 5, 'X'); }
        else { str.insert(str.end(), bits[2], 'X'); }
        
        if(bits[3] == 4) { str += "IV"; }
        else if(bits[3] == 9) { str += "IX"; }
        else if(bits[3] >= 5) { str += "V"; str.insert(str.end(), bits[3] - 5, 'I'); }
        else { str.insert(str.end(), bits[3], 'I'); }

        return str;
    }

    int romanToInt(string s) {
        unordered_map<char, int> char2Value { {'I', 1}, {'V', 5}, {'X', 10}, {'L', 50}, {'C', 100}, {'D', 500}, {'M', 1000} };
        int temp_value = 0, result = 0;
        for(auto iter = s.rbegin(); iter != s.rend(); ++iter) {
            int currValue = char2Value[*iter];
            if(currValue < temp_value) { result -= char2Value[*iter]; }
            else { result += char2Value[*iter]; }
            temp_value = currValue;
        }
        return result;
    }
```

### 其他解法

```cpp
string intToRoman(int num) {
    int values[] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
    string reps[] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
    string res;
    for (int i = 0; i < 13; i ++ )  //这里不使用图里的count了，一遍一遍来就行了
        while(num >= values[i]) {
            num -= values[i];
            res += reps[i];
        }
    return res;
}
```

## [421. 数组中两个数的最大异或值](https://leetcode-cn.com/problems/maximum-xor-of-two-numbers-in-an-array/)

难度：中等

类型：位运算，字典树

### 原题描述

给你一个整数数组 nums ，返回 nums[i] XOR nums[j] 的最大运算结果，其中 0 ≤ i ≤ j < n 。

进阶：你可以在 O(n) 的时间解决这个问题吗？

### 解题思路

- 暴力（超时）

- 前缀数

  把数组的每一个二进制位看作是树的节点。比较的时候，有不同的就走不同的位，就找到了单个的最大，对所有数都进行一轮比较就得到了所有的最大。

### 解题代码

```cpp
struct Trie {
    Trie *left = nullptr;
    Trie *right = nullptr;
};

class Solution {
private:
    Trie *root = new Trie();
    vector<Trie *> trie;
    void add(int num) {
        int numSave = num;
        Trie *curr = root;
        for(int i = 30; i >= 0; --i) {
            if((num >> i) & 1) { 
                if(curr->right == nullptr) { curr->right = new Trie(); }
                curr = curr->right;
            }
            else {
                if(curr->left == nullptr) { curr->left = new Trie(); }
                curr = curr->left;
            }
            trie.push_back(curr);
        }
    }
    int findMax(int num) {
        int numSave = num;
        Trie *curr = root;
        for(int i = 30; i >= 0; --i) {
            if((num >> i) & 1) {
                if(curr->left) { curr = curr->left; numSave |= 1 << i; }
                else { curr = curr->right; numSave &= ~(1U << i); }
            }
            else {
                if(curr->right) { curr = curr->right; numSave |= 1 << i; }
                else { curr = curr->left; numSave &= ~(1U << i); }
            }
        }
        return numSave;
    }
public:
    int findMaximumXOR(vector<int>& nums) {
        int size = nums.size(), result = 0;
        if(size == 1) { return 0; }
        for(int i = 1; i < size; ++i){
            add(nums[i - 1]);
            int temp = findMax(nums[i]);
            if(result < temp) { result = temp; }
        }
        return result;
    }
};
```

