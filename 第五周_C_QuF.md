# 第五周_C_QuF

## I 本周目标

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   6    |   6    |   1    |

## II 本周刷题总结

### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

难度：简单

类型：数学

#### 原题描述

给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。

如果反转后整数超过 32 位的有符号整数的范围 [−2^31,  2^31 − 1] ，就返回 0。

假设环境不允许存储 64 位整数（有符号或无符号）。

#### 解题思路

一位一位的从最低位到最高位进行操作就可以了。具体就是

- 取低位 `x % 10`
- 更新数字 `x /= 10`
- 分正负后进行判断

这里要注意下特殊情况

- x = 0
- x = INT_MIN

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
            x = -x;
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

#### 其他解法

一样的思路，不过更加简洁

```cpp
	int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            if (x > 0 && ans > (INT_MAX - x % 10) / 10) return 0;
            if (x < 0 && ans < (INT_MIN - x % 10) / 10) return 0;
            ans = ans * 10 + x % 10;
            x /= 10;
        }
        return ans;
    }
```

### [1473. 粉刷房子 III](https://leetcode-cn.com/problems/paint-house-iii/)

难度：困难

类型：动态规划

#### 原题描述

在一个小城市里，有 m 个房子排成一排，你需要给每个房子涂上 n 种颜色之一（颜色编号为 1 到 n ）。有的房子去年夏天已经涂过颜色了，所以这些房子不需要被重新涂色。

我们将连续相同颜色尽可能多的房子称为一个街区。（比方说 houses = [1,2,2,3,3,2,1,1] ，它包含 5 个街区  [{1}, {2,2}, {3,3}, {2}, {1,1}] 。）

给你一个数组 houses ，一个 m * n 的矩阵 cost 和一个整数 target ，其中：

houses[i]：是第 i 个房子的颜色，0 表示这个房子还没有被涂色。
cost[i][j]：是将第 i 个房子涂成颜色 j+1 的花费。
请你返回房子涂色方案的最小总花费，使得每个房子都被涂色后，恰好组成 target 个街区。如果没有可用的涂色方案，请返回 -1 。

#### 解题思路

- 暴力DFS + 剪枝 ( 超时 ): 

  按照题目正向思路推导。递归函数传入 当前的房子序号，上一次的房子颜色，社区还剩下多少，还有当前的花费

  这里要考虑两种情况：

  - 当前房子已经涂色：根据上一次的颜色判断社区数量是否要改变，如何序号 + 1，其他不变
  - 当前房子没涂色：根据这次颜色更新花费，根据上一次的颜色判断社区数量是否要改变，如何序号 + 1，其他不变

  退出循环：

  - target减少为0
  - 当前花费大于最小花费（优化）

- 动态规划

  有了DFS的思路就很好想DP了。debug可以发现递归中有很多重复，可以把这些存为一个数组。根据递归的函数可以确定有三个变量：房屋代号，颜色和社区数(target)。然后DP数组中存的是花费。

  通过一个个填DP数组就可以解出来了。
  $$
  painted     : dp[i][j][k] = \min{dp[i - 1][j][k], dp[i - 1][color][k - 1]}\\
  not painted : dp[i][j][k] = \min{dp[i - 1][j][k] + cost[i][j], dp[i - 1][color][k - 1]} + cost[i][j]
  $$

#### 解题代码

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
            if(target == 0) { 
                minCosts = minCosts < currCost ? minCosts : currCost; }
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

```cpp
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
        }	// for special case	
        vector<vector<vector<int>>> dp(m, vector<vector<int>>(n, vector<int>(target + 1, 1000001)));
        if(houses[0] == 0) { for(int i = 0; i < n; ++i) { dp[0][i][1] = cost[0][i]; } }	// init
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
```

