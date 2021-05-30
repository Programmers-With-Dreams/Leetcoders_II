# I 本周目标

最近困难题出现的频率好高啊。。

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   7    |   7    |   1    |

# II 本周刷题总结

## [664. 奇怪的打印机](https://leetcode-cn.com/problems/strange-printer/)

难度 困难

### 原题描述

有台奇怪的打印机有以下两个特殊要求：

打印机每次只能打印由 同一个字符 组成的序列。
每次可以在任意起始和结束位置打印新字符，并且会覆盖掉原来已有的字符。
给你一个字符串 s ，你的任务是计算这个打印机打印它需要的最少打印次数。

### 解题思路

这种题目就是铁的动态规划题了，可以定义动态规划数组 $dp[left][right]=\#paint$

例如 $ababbca$，$result=dp[0][6]=$

- $s[left]=s[right]:dp[left][right]=dp[left][right-1]$
- $s[left]\ne s[right]:dp[left][right]=\min\{dp[left,i]+dp[i][right]\}$

> $dp[i][i+1]=1$

### 解题代码

```cpp
    int strangePrinter(string s) {
        int size = s.size();
        vector<vector<int>> dp(size, vector<int>(size + 1, 0));
        for(int i = size - 1; i >= 0; --i) {
            for(int j = i + 1; j <= size; ++j) {
                if(i + 1 == j) {dp[i][j] = 1;}
                else if(s[i] == s[j - 1]) { dp[i][j] = dp[i][j - 1]; }
                else {
                    dp[i][j] = dp[i][i + 1] + dp[i + 1][j];
                    for(int length = 2; length < j - i; ++length) {
                        int temp = dp[i][i + length] + dp[i + length][j];
                        if(temp < dp[i][j]) { dp[i][j] = temp; }
                    }
                }
            }
        }
        return dp[0][size];
    }
```

## [1190. 反转每对括号间的子串](https://leetcode-cn.com/problems/reverse-substrings-between-each-pair-of-parentheses/)

难度 中等

### 原题描述

给出一个字符串 s（仅含有小写英文字母和括号）。

请你按照从括号内到外的顺序，逐层反转每对匹配括号中的字符串，并返回最终的结果。

注意，您的结果中 不应 包含任何括号。

### 解题方法

看起来简单，但是做起来还挺麻烦的

- 暴力：使用递归，在递归内部使用栈来反转
- 双向数组：当遇到右括号就从数组尾部取出字符，一直到遇到左括号，然后把取出的字符再存进去。
- 预处理法：预先获取括号的位置，存到数组中，遇到了括号就跳到数组对应的位置，然后把迭代的方向反过来

### 解题代码

暴力求解

```cpp
    string result;
	string reverseParentheses(string s) {
        for(auto iter = s.begin(); iter != s.end(); ++iter) {
            if(*iter == '(') { result += reverseString(iter); }
            else { result.push_back(*iter); }
        }
        return result;
    }
    string reverseString(string::iterator &iter) {
        string ans;
        stack<char> charStack;
        ++iter;
        while(*iter != ')') {
            if(*iter == '(') { 
                string temp = reverseString(iter); 
                for(auto c : temp) { charStack.push(c); }
            }
            else { charStack.push(*iter); }
            ++iter;
        }
        while(!charStack.empty()) { ans.push_back(charStack.top()); charStack.pop(); }
        cout << ans << endl;
        return ans;
    }
```

双向链表

```cpp
    string reverseParentheses(string s) {
        deque<char> charDeque;
        int size = s.size();
        for(int i = 0; i < size; ++i) {
            if(s[i] == ')') {
                string temp;
                while (charDeque.back() != '(') { temp += charDeque.back(); charDeque.pop_back(); }
                charDeque.pop_back();
                for (auto c : temp) { charDeque.push_back(c); }
            }
            else { charDeque.push_back(s[i]); }
        }
        return string(charDeque.begin(), charDeque.end());
    }
```

预处理法

```cpp
    string reverseParentheses(string s) {
        // init '(' and ')'
        int size = s.size();
        stack<int> left;
        vector<int> peer(size, -1);
        for(int i = 0; i < size; ++i) {
            switch(s[i]) {
            case '(': left.push(i); break;
            case ')': 
                peer[left.top()] = i; 
                peer[i] = left.top(); 
                left.pop(); break; 
            }
        }
        // do reverse
        string result;
        int step = 1;
        for(int i = 0; i < size; ) {
            switch(s[i]) {
            case '(': case ')':
                step *= -1; i = peer[i] + step;
                break; 
            default: result += s[i]; i += step;
            }
        }
        return result;
    }
```

## [461. 汉明距离](https://leetcode-cn.com/problems/hamming-distance/)

简单

### 原题描述

两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 x 和 y，计算它们之间的汉明距离。

注意：
0 ≤ x, y < 2^31.

### 解题思路

就是求这两个异或后的数 1 出现的次数

### 解题代码

```cpp
    int hammingDistance(int x, int y) {
        int temp = x ^ y, count = 0;
        if(!temp) { return 0; }
        for(int i = 30; i >= 0; --i) {
            if(temp & (1 << i)) { ++count; }
        }
        return count;
    }
```

下面这种方法优化了一些时间

```cpp
    int hammingDistance(int x, int y) {
        int temp = x ^ y, count = 0;
        while(temp) { temp &= (temp - 1); ++count; }
        return count;
    }
```

## [1787. 使所有区间的异或结果为零](https://leetcode-cn.com/problems/make-the-xor-of-all-segments-equal-to-zero/)

困难

### 原题描述

给你一个整数数组 nums 和一个整数 k 。区间 [left, right]（left <= right）的 异或结果 是对下标位于 left 和 right（包括 left 和 right ）之间所有元素进行 XOR 运算的结果：nums[left] XOR nums[left+1] XOR ... XOR nums[right] 。

返回数组中 要更改的最小元素数 ，以使所有长度为 k 的区间异或结果等于零。

### 解题思路

经过实验可以发现，要想实现题目的效果，就必须使得 $nums[i]=nums[i+k]$ 。原来的数组就被分组：

```
3,4,5,2,1,7,3,4,7,8 ==>
3,4,5
2,1,7
3,4,7
8
```

其中，每一列的数字更改后都必须相同。因此，可以用map来记录数据出现的次数。

然后可以发现，只要第一组数据出来了就都出来了。下面的就是动态规划的内容了。第一组的数可以根据题目限制，0 <= nums[i] < 2^10 = 1024，因此最多有 1024*k 种情况，

定义 $dp[currIndx][xor]=\#changes$，最终要求的结果是 $dp[k-1][0]$

- $dp[0][i]=size-count[i]$

- $dp[curr][xor\Lambda i]=\min\{ dp[curr][i]+size(curr)-count[i],\min dp[curr-1]+size\}$

### 解题代码

```cpp
class Solution {
public:
    int minChanges(vector<int>& nums, int k) {
        // dp[#column][xors] = # changedvalue -> dp[k - 1][0]
        // first column: map<value, count>
        //      dp[0][value] = size - count;
        // other column: map<value, count>
        //      dp[i][values^value] = min{ dp[i - 1][values] + size - count }
        int size = nums.size();
        vector<vector<int>> columns(k, vector<int>());
        vector<vector<int>> dp(k, vector<int>(1024, INT_MAX));
        vector<int> minOfColumn(k, INT_MAX);
        for(int i = 0; i < size; ++i) { columns[i%k].push_back(nums[i]); }
        for(int i = 0; i < k; ++i) {
            unordered_map<int, int> value2count;
            for(auto item : columns[i]) { ++value2count[item]; }
            int columnSize = columns[i].size();
            if(i) {
                for (int xors = 0; xors < 1024; ++xors) {
                    dp[i][xors] = minOfColumn[i - 1] + columnSize;
                    for(auto pair : value2count) {
                        int temp = dp[i - 1][xors ^ pair.first] + columnSize - pair.second;
                        if(temp < dp[i][xors]) { dp[i][xors] = temp; }
                    }
                    if(dp[i][xors] < minOfColumn[i]) { minOfColumn[i] = dp[i][xors]; }
                }
            }
            else {
                for (int xors = 0; xors < 1024; ++xors) {
                    dp[i][xors] = columnSize - value2count[xors];
                    if(dp[i][xors] < minOfColumn[i]) { minOfColumn[i] = dp[i][xors]; }
                }
            }
        }
        return dp[k-1][0];
    }
};
```

## [477. 汉明距离总和](https://leetcode-cn.com/problems/total-hamming-distance/)

中等

### 原题描述

两个整数的 [汉明距离](https://baike.baidu.com/item/汉明距离/475174?fr=aladdin) 指的是这两个数字的二进制数对应位不同的数量。

给你一个整数数组 `nums`，请你计算并返回 `nums` 中任意两个数之间汉明距离的总和。

### 解题思路

- 暴力求解 两层for 循环

- 按位求和

  例如 [4,14,4]

  ```
  bit of 4:			0100
  bit of 14:			1110
  bit of 4:			0100
  a=sum:				1310
  b=(size-sum)%size:	2020
  ans=sum{a*b}:		2244
  ```

### 解题代码

暴力（超时）

```cpp
    int totalHammingDistance(vector<int>& nums) {
        int size = nums.size(), count = 0;
        for(int i  = 0; i < size - 1; ++i) {
            for(int j = i + 1; j < size; ++j) {
                int flag = nums[i] ^ nums[j];
                while(flag) { flag &= flag - 1; ++count; }
            }
        }
        return count;
    }
```

按位求和

```cpp
    int totalHammingDistance(vector<int>& nums) {
        int size = nums.size(), count = 0;
        vector<int> bitCount(31, 0);
        for(auto num : nums) {
            for(int i = 0; i < 31; ++i) {
                if(num & (1 << i)) { ++bitCount[i]; }
            }
        }
        for(auto bit : bitCount) {
            count += bit * ((size - bit) % size);
        }
        return count;
    }
```

## [231. 2 的幂](https://leetcode-cn.com/problems/power-of-two/)

简单

### 原题描述

给你一个整数 n，请你判断该整数是否是 2 的幂次方。如果是，返回 true ；否则，返回 false 。

如果存在一个整数 x 使得 n == 2^x ，则认为 n 是 2 的幂次方。

### 解题思路

暴力求解

### 解题代码

```cpp
    bool isPowerOfTwo(int n) {
        if(n <= 0) { return false; }
        int count = 0;
        while(n) { n &= n - 1; ++count; }
        return count > 1 ? false : true;
    }
```

## [1074. 元素和为目标值的子矩阵数量](https://leetcode-cn.com/problems/number-of-submatrices-that-sum-to-target/)

困难

### 原题描述

给出矩阵 matrix 和目标值 target，返回元素总和等于目标值的非空子矩阵的数量。

子矩阵 x1, y1, x2, y2 是满足 x1 <= x <= x2 且 y1 <= y <= y2 的所有单元 matrix\[x][y] 的集合。

如果 (x1, y1, x2, y2) 和 (x1', y1', x2', y2') 两个子矩阵中部分坐标不同（如：x1 != x1'），那么这两个子矩阵也不同。

### 解题思路

- 四层 for 循环暴力求解
- 利用hash简化为三层 for 循环

### 解题代码

```cpp
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        int sizeR = matrix.size(), sizeC = matrix.at(0).size(), count = 0;
        vector<vector<int>> dp(sizeR + 1, vector<int>(sizeC + 1, 0));
        for(int i = 1; i <= sizeR; ++i) {
            for(int j = 1; j <= sizeC; ++j) {
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1] + matrix[i - 1][j - 1];
            }
        }
        for(int l = 0; l < sizeC; ++l) {
            for(int r = l + 1; r <= sizeC; ++r) {
                for(int u = 0; u < sizeR; ++u) {
                    for(int d = u + 1; d <= sizeR; ++d) {
                        if(dp[d][r] - dp[d][l] - dp[u][r] + dp[u][l] == target) { ++count; }
                    }
                }
            }
        }
        return count;
    }
```

```cpp
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        // get prex sum
        int sizeR = matrix.size(), sizeC = matrix.at(0).size(), count = 0;
        vector<vector<int>> prexSum(sizeR + 1, vector<int>(sizeC + 1, 0));
        for(int i = 1; i <= sizeR; ++i) {
            for(int j = 1; j <= sizeC; ++j) {
                prexSum[i][j] = matrix[i - 1][j - 1] + prexSum[i - 1][j] + prexSum[i][j - 1] - prexSum[i - 1][j - 1];
            }
        }
        // count
        for(int u = 0; u < sizeR; ++u) {
            for(int d = u + 1; d <= sizeR; ++d) {
                unordered_map<int, int> area2Count;
                for(int r = 1; r <= sizeC; ++r) {
                    int curr = prexSum[d][r] - prexSum[u][r];
                    if(curr == target) { ++count; }
                    count += area2Count[curr - target];
                    ++area2Count[curr];
                }
            }
        }
        return count;
    }
};
```

