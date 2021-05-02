# 第二周_C_QuF

## I 本周目标

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   6    |   6    |   1    |

## II 本周刷题总结

### 第0题 [剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)、[剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

难度：中等

类型：`递归`

#### a. 原题陈述

老题目了，这题不算。写一个函数，输入 `n` ，求斐波那契（Fibonacci）数列的第 `n` 项（即 `F(N)`）。

#### b. 解题思路

动态规划

#### c. 解题代码

```cpp
    int fib(int n) {
        if(n == 0) { return 0; }
        int fprev = 0, fcurr = 1;
        while(--n > 0){ fcurr += fprev; fprev = fcurr - fprev ;fcurr %= 1000000007;}
        return fcurr;
    }
    int numWays(int n) {
        int fprev = 1, fcurr = 1;
        while(--n > 0){ fcurr += fprev; fprev = fcurr - fprev ;fcurr %= 1000000007;}
        return fcurr;
    }
```

### 第1题 [剑指 Offer 11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

难度：简单

类型：`二分查找`

#### a. 原题陈述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

#### b. 解题思路

二分比较后分情况讨论可以删掉一块区域。

```
▆▇▉▁▂▃▅(常规情况)
▉▉▉▁▉▉▉(极端情况)
```

|  min  |  mid  |  max  |      说明       |
| :---: | :---: | :---: | :-------------: |
| begin |  mid  |  end  |  [mid,end]没有  |
| begin |  end  |  mid  |     不存在      |
|  mid  | begin |  end  |     不存在      |
|  mid  |  end  | begin |  [mid,end]没有  |
|  end  | begin |  mid  | [begin,mid]没有 |
|  end  |  mid  | begin |     不存在      |

#### c. 解题代码

```cpp
int findMin(vector<int>& nums) {
    int begin = 0, end = nums.size() - 1;
    while(begin + 1 < end) {
        int mid = (begin + end) >> 1;
        if(nums[begin] <= nums[mid] && nums[mid] <= nums[end]){ end -= 1; }	//处理极端情况
        else if(nums[mid] <= nums[end] && nums[end] <= nums[begin]){ end = mid; }
        else if(nums[end] <= nums[begin] && nums[begin] <= nums[mid]){ begin = mid;}
        else { return -1; }	//为了增加代码鲁棒性
    }
    if(begin + 1 == end){ return nums[begin] < nums[end] ? nums[begin] : nums[end];}
    else return nums[begin];
    }
```

### 第2题 [剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

难度：中等

类型：`dfs`

#### a. 原题陈述

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

#### b. 解题思路

很标准的DFS了

#### c. 解题代码

```cpp
bool dfs(vector<vector<char>>& board, string &word, vector<vector<bool>> &visit, pair<int, int> index, int depth){
    if(word.size() == depth + 1) { return true; }
    int i = index.first, j = index.second;
    visit[i][j] = true;
    // 下面这 4 个 if 也可以写在一起
    if(0 < i && !visit[i - 1][j] && board[i - 1][j] == word[depth + 1]) { if(dfs(board, word, visit, {i - 1, j}, depth + 1)) { return true; } }
    if(i < visit.size() - 1 && !visit[i + 1][j] && board[i + 1][j] == word[depth + 1]) { if(dfs(board, word, visit, {i + 1, j}, depth + 1)) { return true; } }
    if(0 < j && !visit[i][j - 1] && board[i][j - 1] == word[depth + 1]) { if(dfs(board, word, visit, {i, j - 1}, depth + 1)) { return true; } }
    if(j < visit.at(0).size() - 1 && !visit[i][j + 1] && board[i][j + 1] == word[depth + 1]) { if(dfs(board, word, visit, {i, j + 1}, depth + 1)) { return true; } }
    visit[i][j] = false;
    return false;
}
bool exist(vector<vector<char>>& board, string word) {
    int sizeR = board.size(), sizeC = board.at(0).size();
    for(int i = 0; i < sizeR; ++i) {
        for(int j = 0; j < sizeC; ++j) {
            if(board[i][j] != word.at(0)) { continue; }
            else {
                vector<vector<bool>> visit(sizeR, vector<bool>(sizeC, false));
                if(dfs(board, word, visit, {i, j}, 0)) { return true; }
                else { continue; }
    } } }
    return false;
}
```

### 第3题 [剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

难度：中等

类型：dfs

#### a. 原题描述

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

#### b. 解题思路

一开始想复杂了，以为要用数学方法优化时间，结果没想到用DFS就够了，画一个图就够了

![Week3T3](https://github.com/Programmers-With-Dreams/Leetcoders_II/blob/C_QuF/Week3T3.png)

#### c. 解题代码

```cpp
	int getBitSum(int x, int y) {
        int sum = 0;
        for(; x; x/=10) { sum += x % 10; }
        for(; y; y/=10) { sum += y % 10; }
        return sum;
    }
    int movingCount(int m, int n, int k) {
        int count = 0;
        vector<vector<bool>> state(m, vector<bool>(n, false)); 
        stack<pair<int, int>> dfsStack;
        dfsStack.push({0, 0});
        while(!dfsStack.empty()) {
            auto currIndex = dfsStack.top();
            int x = currIndex.first, y = currIndex.second;
            dfsStack.pop();
            state[x][y] = true;
            ++count;
            if(x - 1 >= 0 && !state[x - 1][y] && getBitSum(x - 1, y) <= k) { 
                dfsStack.push({x - 1, y}); state[x - 1][y] = true; }
            if(y - 1 >= 0 && !state[x][y - 1] && getBitSum(x, y - 1) <= k) { 
                dfsStack.push({x, y - 1}); state[x][y - 1] = true; }
            if(x + 1 < m && !state[x + 1][y] && getBitSum(x + 1, y) <= k) { 
                dfsStack.push({x + 1, y}); state[x + 1][y] = true; }
            if(y + 1 < n && !state[x][y + 1] && getBitSum(x, y + 1) <= k) { 
                dfsStack.push({x, y + 1}); state[x][y + 1] = true; }
        }
        return count;
    }
```

### 第4题 [剑指 Offer 14- I. 剪绳子](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

难度：中等

类别：数学、动态规划

#### a. 原题描述

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

#### b. 解题思路

动态规划！
$$
f(n)=\max\left({i\times f(n-i)}\right)
$$

#### c. 解题代码

```cpp
	int cuttingRope(int n) {
        if(n < 4) { return n - 1; }
        vector<int> dp(n + 1, 0); dp[2] = 2; dp[3] = 3;	// 这里有些坑
        for(int i = 3; i <= n; ++i) {
            for(int j = 1; j < i; ++j) {
                int temp = j * dp[i - j];
                if(dp[i] < temp) { dp[i] = temp; }
            }
        }
        return dp[n];
    }
```

#### d. 其他解法

数学推导无敌，能整除 3 就用 3 ，到 4 了就换成两个2

```cpp
	int cuttingRope(int n) {
    	return n <= 3? n - 1 : pow(3, n / 3) * 4 / (4 - n % 3);
	}
```

### 第5题 [剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

难度：简单

类型：位运算

#### a. 原题描述

请实现一个函数，输入一个整数（以二进制串形式），输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

#### b. 解题思路

移位相加即可

#### c. 解题代码

```cpp
    int hammingWeight(uint32_t n) {
        int count = 0;
        for(; n; n >>= 1){ count += (n & 1); }
        return count;
    }
```

#### d. 其他解法

这种时间上还要更快。n &= (n - 1) 相当于把 n 的最后一个 1 变为了 0

```cpp
    int hammingWeight(uint32_t n) {
        int count = 0;
        while(n) { ++count; n &= (n - 1); }
        return count;
    }
```



### 第6题 [剑指 Offer 16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

难度：中等

类型：递归

#### a. 原题描述

实现 [pow(*x*, *n*)](https://www.cplusplus.com/reference/valarray/pow/) ，即计算 x 的 n 次幂函数（即，x^n）。不得使用库函数，同时不需要考虑大数问题。

#### b. 解题思路

pow 可以和位运算联系起来。例如18^17。17的二进制是10001。因此，18^17 = 18^{2^4} * 18^{2^0} = 18^16 * 18

#### c. 解题代码

```cpp
    double myPow(double x, int n) {
        double result = 1, currPow = x;
        if(n == 0) { return 1; }
        if(x == 1) { return 1; }
        else if(n > 0) {
            for(unsigned int flag = n; flag; flag >>= 1) {
                if(flag & 1) { result *= currPow; }
                currPow *= currPow;
            }
            return result;
        }
        else {
            unsigned int flag;
            if(n != -2147483648) { flag = -n; }
            else { flag = 2147483648; }
            for(; flag; flag >>= 1) {
                if(flag & 1) { result *= currPow; }
                currPow *= currPow;
            }
            return 1.0 / result;
        } // 这里可以优化变得更短
    }
```