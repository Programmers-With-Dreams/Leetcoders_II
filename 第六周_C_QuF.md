# 第六周_C_QuF

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   7    |   6    |  > 1   |

[TOC]

## [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

难度：简单

类型：数学

#### 原题描述

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−2^31,  2^31 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。

#### 解题思路

就是把每一位取出来，再颠倒的乘起来。主要的难点就是要防止溢出。

这个在成的时候判断下就可以了（num * d + b > INT_MAX => num > (INT_MAX - b)/d ）

特殊情况就是 x == 0的时候，单独判断下就可以了

#### 解题代码

```cpp
    int reverse(int x) {
        int result = 0;
        if(x == INT_MIN || !x) { return 0; }
        else if(x > 0) {
            for(; x; x /= 10) {
                int bit = x % 10;
                if(result <= (INT_MAX - bit) / 10) { result = result * 10 + bit; }
                else { return 0; }
            }
        }
        else {
            x = -x;	// INT_MIN 在这一步会溢出
            for(; x; x /= 10) {
                int bit = x % 10;
                if(result <= (INT_MAX - bit) / 10) { result = result * 10 + bit; }
                else { return 0; }
            }
            result = -result;
        }
        return result;
    }
```

## [1473. 粉刷房子 III](https://leetcode-cn.com/problems/paint-house-iii/)

难度：困难

类型：动态规划

#### 原题描述

在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不可以被重新涂色。

我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = [1,2,2,3,3,2,1,1] ，它包含 5 个街区  [{1}, {2,2}, {3,3}, {2}, {1,1}] 。）

给你一个数组 houses ，一个 m * n 的矩阵 cost 和一个整数 target ，其中：

houses[i]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
cost[i][j]：是将第 i 个房子涂成颜色 j+1 的花费。
请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。

#### 解题思路

- 回溯 + 剪枝（超时）

  一个个试出来，遇到不成立的情况就退出。当前涂色的房子、到当前涂色房子为止的社区数量和这次房子的颜色会影响到递归的结果。

  成功的情况：遍历到最后一个房子，此时剩余社区数target=0

  一般情况：分为

  - 当前房子已经涂色：根据房子颜色和上一个房子的颜色判断target是否减少
  - 当前房子没有涂色：选择颜色，把当前的花费和target带入下一次迭代

  可以剪枝的情况：

  - 当前的花费超过遍历后的最小花费
  - target == 0，有涂色的房子，而且涂色不同

- 记忆化搜索：运行上述代码可以发现，target, index, lastColor 这三个数在递归中重复计算了

- 动态规划：有了记忆化搜索，DP的状态方程就不难想象了。这两个 min 第一个是相同颜色，第二个是不同颜色

  - 涂装过：$f[index][color][target]=\min\left\{ \begin{array}{l}
    	f[index - 1][color][target]\\
    	f[index-1][Color_i][target-1]\\
    \end{array} \right.$

  - 没涂装过：$f[index][color][target]=\min\left\{ \begin{array}{l}
    	f[index - 1][color][target]+cost[index][color]\\
    	f[index-1][Color_i][target-1]+cost[index][color]\\
    \end{array} \right.$

    > 这里的target是到index目前为止的社区数，和上面还剩多少社区数有些不同

#### 解题代码

回溯 + 剪枝 (超时)

```cpp
class Solution {
public:
    int minCost(vector<int>& houses, vector<vector<int>>& cost, int m, int n, int target) {
        costRec(houses, cost, 0, target, 0, 0);
        return minCosts == 1000001 ? -1 : minCosts;
    }
private:
    int minCosts = 1000001;
    void costRec(vector<int>& houses, vector<vector<int>>& cost, int index, int target, int currCost, int lastColor) {
        if(minCosts < currCost) { return; }
        if(houses.size() == index) { 
            if(target == 0) { minCosts = minCosts < currCost ? minCosts : currCost; }
            return; 
        }
        if(target == 0) {
            if(houses.at(index) == 0){
                costRec(houses, cost, index + 1, target, currCost + cost.at(index).at(lastColor - 1), lastColor);
            }
            else if(lastColor != houses.at(index)) { return; }
            else { costRec(houses, cost, index + 1, target, currCost, lastColor); }
        }
        else{
            if(houses.at(index)) {
                if(lastColor == houses.at(index)) { costRec(houses, cost, index + 1, target, currCost, houses.at(index)); }
                else { costRec(houses, cost, index + 1, target - 1, currCost, houses.at(index)); }
            }
            else {
                int colorSize = cost.at(0).size();
                for(int i = 0; i < colorSize; ++i) {
                    if(lastColor == i + 1) { costRec(houses, cost, index + 1, target, currCost + cost.at(index).at(i), i + 1); }
                    else { costRec(houses, cost, index + 1, target - 1, currCost + cost.at(index).at(i), i + 1); }
                }
            }
        }
    }
};
```

#### 其他代码

DP

```cpp
class Solution {
public:
    int minCost(vector<int>& houses, vector<vector<int>>& cost, int m, int n, int target) {
        // index, color, #blocks -> minCost
        // - painted     : dp[i][j][k] = min{dp[i - 1][j][k], dp[i - 1][color][k - 1]}
        // - not painted : dp[i][j][k] = min{dp[i - 1][j][k] + cost[i][j], dp[i - 1][color][k - 1]} + cost[i][j]
        // getMinCost: min dp[m]
        int tempTarget = 1;
        for(int i = 0; i < m; ++i) {
            if(houses[i] == 0) { break; }
            if(i && houses[i] != houses[i - 1]) { ++tempTarget; }
            if(target < tempTarget) { return -1; }
        }
        vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(target + 1, 1000001)));
        if(houses[0] == 0) { for(int i = 0; i < n; ++i) { dp[0][i][1] = cost[0][i]; } }
        else { dp[0][houses[0] - 1][1] = 0; }
        for(int i = 1; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                for(int k = 1; k <= target; ++k) {
                    if(i + 1 < k) { break; }
                    if(houses[i] == 0) {
                        dp[i][j][k] = dp[i - 1][j][k] + cost[i][j];  // same color with prev
                        for(int color = 0; color < n; ++color) {
                            if(color == j) { continue; }
                            int temp = dp[i - 1][color][k - 1] + cost[i][j]; 
                            if(temp < dp[i][j][k]) { dp[i][j][k] = temp; }
                        }
                    }   // not painted
                    else {
                        if(j + 1 != houses[i]) { continue; }
                        dp[i][j][k] = dp[i - 1][j][k];  // same color with prev
                        for(int color = 0; color < n; ++color) {
                            if(color == j) { continue; }
                            int temp = dp[i - 1][color][k - 1]; 
                            if(temp < dp[i][j][k]) { dp[i][j][k] = temp; }
                        }
                    }   // painted
                }
            }
        }
        int result = 1000001;
        for(int i = 0; i < n; ++i) { if(dp[m - 1][i][target] < result) { result = dp[m - 1][i][target]; } }
        return result >= 1000001 ? -1 : result;
    }
};
```

## [740. 删除并获得点数](https://leetcode-cn.com/problems/delete-and-earn/)

难度：中等

类型：动态规划

#### 原题描述

给你一个整数数组 nums ，你可以对它进行一些操作。

每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除所有 等于 nums[i] - 1 和 nums[i] + 1 的元素。

开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

#### 解题思路

可以先把数组排个序，这样后面会方便些。然后可以发现所求的结果和 **每个数×它出现的次数** 息息相关。

因此可以把这个数存成一个数组 sum（本来是要存成hash的，但是题目中出现了nums[i] - 1 和 nums[i] + 1 ）。因此数组更好。

然后可以发现状态方程
$$
f[i]=\max\{ f[i-1],f[i-2]+sum[i-1]\}
$$
$f[i-1]$ 表示删去 i , 后面那个表示删去 i

i 的取值范围是 nums 中最小的，到所有 nums 中最大的

#### 解题代码

```cpp
class Solution {
public:
    int deleteAndEarn(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int sumSize = nums.at(nums.size() - 1) - nums.at(0) + 1;
        vector<int> sum(sumSize, 0);
        for(auto num : nums) { sum[num - nums.at(0)] += num; }
        vector<int> dp(sumSize + 1, 0);
        dp[0] = 0, dp[1] = sum.at(0);
        for(int i = 2; i <= sumSize; ++i) {
            dp[i] = (dp[i - 1] < dp[i - 2] + sum[i - 1]) ? (dp[i - 2] + sum[i - 1]) : dp[i - 1];
        }
        return dp[sumSize];
    }
};
```

## [1720. 解码异或后的数组](https://leetcode-cn.com/problems/decode-xored-array/)

难度：简单

类型：位运算

### 原题描述

未知 整数数组 arr 由 n 个非负整数组成。

经编码后变为长度为 n - 1 的另一个整数数组 encoded ，其中 encoded[i] = arr[i] XOR arr[i + 1] 。例如，arr = [1,0,2,1] 经编码后得到 encoded = [1,2,3] 。

给你编码后的数组 encoded 和原数组 arr 的第一个元素 first（arr[0]）。

请解码返回原数组 arr 。可以证明答案存在并且是唯一的。

### 解题思路

一个一个异或回去

### 解题代码

```cpp
    vector<int> decode(vector<int>& encoded, int first) {
        vector<int> result { first };
        int size = encoded.size();
        for(int i = 0; i < size; ++i) {
            result.push_back(result[i] ^ encoded[i]);
        }
        return result;
    }
```

## [1486. 数组异或操作](https://leetcode-cn.com/problems/xor-operation-in-an-array/)

难度：简单

类型：位运算

### 原题描述

给你两个整数，n 和 start 。

数组 nums 定义为：nums[i] = start + 2*i（下标从 0 开始）且 n == nums.length 。

请返回 nums 中所有元素按位异或（XOR）后得到的结果。

### 解题代码

```cpp
    int xorOperation(int n, int start) {
        int result = 0;
        for(int i = 0; i < n; ++i) { result ^= start + 2*i; }
        return result;
    }
```

## [1723. 完成所有工作的最短时间](https://leetcode-cn.com/problems/find-minimum-time-to-finish-all-jobs/)

难度：困难

类型：递归、回溯

### 原题描述

给你一个整数数组 jobs ，其中 jobs[i] 是完成第 i 项工作要花费的时间。

请你将这些工作分配给 k 位工人。所有工作都应该分配给工人，且每项工作只能分配给一位工人。工人的 工作时间 是完成分配给他们的所有工作花费时间的总和。请你设计一套最佳的工作分配方案，使工人的 最大工作时间 得以 最小化 。

返回分配方案中尽可能最小的最大工作时间 。

### 解题思路

这题综合了对结果二分搜索和回溯，挺经典的。

首先最小的最大工作时间的最小值是 jobs 中最大的那个数，最大值是 jobs 的总和（一个人干所有）

因此就可以对结果进行二分搜索。二分搜索中最关键的函数是判断这个时间可不可行。

判断可以不可行就需要用到回溯 + 剪枝了

### 解题代码

```cpp
    int minimumTimeRequired(vector<int>& jobs, int k) {
        sort(jobs.rbegin(), jobs.rend());
        int begin = jobs.at(0), end = 0;
        for(auto job : jobs) { end += job; }
        vector<int> temp(k, 0);
        while(begin < end) {
            int mid = (begin + end) >> 1;
            if(Required(jobs, k, mid, 0, temp)) { end = mid; }
            else { begin = mid + 1; }
        }
        return begin;
    }
    bool Required(vector<int>& jobs, int k, int time, int index, vector<int> &times) {
        if(jobs.size() <= index) { return true; }
        bool result = false;
        for(int i = 0; i < k; ++i) {
            if(times[i] + jobs[index] <= time) {
                times[i] = times[i] + jobs[index];
                if(Required(jobs, k, time, index + 1, times)) { 
                    times[i] = times[i] - jobs[index]; 
                    return true; 
                }
                times[i] = times[i] - jobs[index];
            }
            if(times[i] == 0) { break; }
        }
        return result;
    }
```

### 其他方法

动态规划 + 状态压缩

这种好难理解啊

```cpp
    int minimumTimeRequired(vector<int>& jobs, int k) {
        int n = jobs.size();
        vector<int> sum(1 << n);
        for (int i = 1; i < (1 << n); i++) {
            int x = __builtin_ctz(i), y = i - (1 << x);
            sum[i] = sum[y] + jobs[x];
        }
        vector<vector<int>> dp(k, vector<int>(1 << n));
        for (int i = 0; i < (1 << n); i++) { dp[0][i] = sum[i]; }
        for (int i = 1; i < k; i++) {
            for (int j = 0; j < (1 << n); j++) {
                int minn = INT_MAX;
                for (int x = j; x; x = (x - 1) & j) {
                    minn = min(minn, max(dp[i - 1][j - x], sum[x]));
                }
                dp[i][j] = minn;
            }
        }
        return dp[k - 1][(1 << n) - 1];
    }
```

## [1482. 制作 m 束花所需的最少天数](https://leetcode-cn.com/problems/minimum-number-of-days-to-make-m-bouquets/)

难度：中等

类型：数组、二分搜索

### 原题描述

给你一个整数数组 bloomDay，以及两个整数 m 和 k 。

现需要制作 m 束花。制作花束时，需要使用花园中 相邻的 k 朵花 。

花园中有 n 朵花，第 i 朵花会在 bloomDay[i] 时盛开，恰好 可以用于 一束 花中。

请你返回从花园中摘 m 束花需要等待的最少的天数。如果不能摘到 m 束花则返回 -1 。

### 解题思路

对结果进行二分搜索

### 解题代码

```c++
    int minDays(vector<int>& bloomDay, int m, int k) {
        int begin = INT_MAX, end = 0;
        if(bloomDay.size() < m * k) { return -1; }
        for(auto day : bloomDay) {
            if(day < begin) { begin = day; }
            if(end < day) { end = day; }
        }
        while(begin < end) {
            int mid = (begin + end) >> 1;
            if(check(bloomDay, m, k, mid)) { end = mid; }
            else { begin = ++mid; }
        }
        if(check(bloomDay, m, k, begin)) { return begin; }
        else { return -1; }
    }
    bool check(vector<int> &bloomDay, int m, int k, int day){
        int count = 0, size = bloomDay.size();
        for(int i = 0; i < size; ++i) {
            if(size - i < m * k - count) { return false; }
            if(day < bloomDay[i]) { count = 0; }
            else { if(++count >= k) { --m; count %= k; } }
        }
        if(m > 0) { return false; }
        else { return true; }
    }
```



