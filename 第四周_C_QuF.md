# 第四周_C_QuF

## I 本周目标

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   6    |   6    |        |

## II 本周刷题总结

### 第一题 [1011. 在 D 天内送达包裹的能力](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days/)

难度：中等

类型：数值，二分查找

#### a. 原题描述

传送带上的包裹必须在 D 天内从一个港口运送到另一个港口。

传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。

返回能在 D 天内将传送带上的所有包裹送达的船的最低运载能力。

#### b. 解题思路

这个题目的特点是正着退很麻烦，但是倒着从结果验证很容易。和一般的二分搜索不同，这个是对结果进行二分搜索。结果的取值范围是 $[\max{weights[i]},\sum weights[i]]$ 。

通过这个范围，判断中间值 $mid=(\max{weights[i]},\sum weights[i]) >> 1$ 是否可以通过

- 可以通过：$begin=mid$
- 不能通过：$end=mid$

#### c. 解题代码

```cpp
    bool isValid (vector<int>& weights, int D, int maxLoad) {
        int currLoad = 0;
        for(auto iter = weights.begin(); iter != weights.end(); ++iter){
            if(maxLoad < *iter) { return false; }
            currLoad += *iter;
            if(currLoad > maxLoad) { currLoad = *iter; --D; }
        }
        if(D > 0) { return true; }
        else { return false; }
    }
    int shipWithinDays(vector<int>& weights, int D) {
        int left = 0, right = 0;
        for(auto iter = weights.begin(); iter != weights.end(); ++iter){
            if(left < *iter) { left = *iter; }
            right += *iter;
        }
        while(left + 1 < right) {
            int mid = (left + right) >> 1;
            if(isValid(weights, D, mid)) { right = mid; }
            else{ left = mid; }
        }
        if(isValid(weights, D, left)){ return left; }
        else { return right; }
    }
```

时间复杂度：$O(n\lg \sum widget[i])$

空间复杂度：$O(1)$

### 第〇题 [938. 二叉搜索树的范围和](https://leetcode-cn.com/problems/range-sum-of-bst/)

难度：简单

类型：树、dfs、递归

这题之前写过了，就不算了

#### a. 原题描述

给定二叉搜索树的根结点 `root`，返回值位于范围 *`[low, high]`* 之间的所有结点的值的和。

#### b. 解题思路

就是很普通的dfs

#### c. 解题代码

```c++
int rangeSumBST(TreeNode* root, int low, int high) {
    int leftSum = 0, rightSum = 0;
    if(root->left)  { leftSum  += rangeSumBST(root->left , low, high);  }
    if(root->right) { rightSum += rangeSumBST(root->right, low, high); }
    if(low <= root->val && root->val <= high) { return leftSum + root->val + rightSum; }
    else { return leftSum + rightSum; }
}
```

```cpp
int rangeSumBST(TreeNode* root, int low, int high) {
    stack<TreeNode *> treeNodeStack; treeNodeStack.push(root); int sum = 0;
    while (!treeNodeStack.empty()){
        TreeNode *currNode = treeNodeStack.top(); treeNodeStack.pop();
        if (low <= currNode->val && currNode->val <= high) { sum += currNode->val; }
        if (currNode->left  && low < currNode->val ) { treeNodeStack.push(currNode->left ); }
        if (currNode->right && currNode->val < high) { treeNodeStack.push(currNode->right); }
    }
    return sum;
}
```

### 第二题 [633. 平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/)

难度：中等

类型：数学

#### a. 原题描述

给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 `a2 + b2 = c` 。

#### b. 解题思路

若 a < b , b的取值范围是 $[\sqrt{c/2},\sqrt{c}]$ ，遍历即可

#### c. 解题代码

```c++
    bool judgeSquareSum(int c) {
        int low = pow(c/2, 0.5), high = pow(c, 0.5);
        for(int i = low; i <= high; ++i) {
            int b = pow(c - (i * i), 0.5);
            if(i * i + b * b == c) { return true; }
        }
        return false;
    }
```

#### d. 其他解法

https://leetcode-cn.com/problems/sum-of-square-numbers/solution/shuang-zhi-zhen-de-ben-zhi-er-wei-ju-zhe-ebn3/

官方双指针的解法挺不错的

```cpp
class Solution {
public:
    bool judgeSquareSum(int c) {
        long left = 0, right = (int)sqrt(c);
        while (left <= right) {
            long sum = left * left + right * right;
            if (sum == c) { return true; } 
            else if (sum > c) { right--; } 
            else { left++; }
        }
        return false;
    }
};
```

### 第三题 [137. 只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)

难度：中等

类型：位运算

#### 原题描述

给你一个整数数组 `nums` ，除某个元素仅出现 **一次** 外，其余每个元素都恰出现 **三次 。**请你找出并返回那个只出现了一次的元素。

####  解题思路

- 把数组中的元素存进 hash 表中，记录出现次数，存完之后在遍历一遍，找出次数为 1 的元素
- 排序，只出现一次的元素左右两边都是不同的值
- 位运算：这一题的位运算十分的巧妙。对每一位，可以把他们按位相加，每一位都存为一个十进制数。这样出现 3 次的元素，每一位都是3 的倍数。例如 `[5,5,5,6]`，按位相加是 `101+101+101+110=413` 。然后对每一位取 3 的余数，就排除掉所有出现 3 次的数字了。

#### 解题代码

```cpp
    int singleNumber(vector<int>& nums) {
        unordered_map<int, int> valueCount;
        for(auto num : nums) { ++valueCount[num]; }
        for(auto [num, count] : valueCount) { if(count == 1) { return num; } }
        return 0;
    }
```

```cpp
int singleNumber(vector<int>& nums) {
    if(nums.empty()) { return 0; }
    sort(nums.begin(), nums.end());
    int size = nums.size();
    if(size == 1) { return nums.at(0); }
    if(size >= 2 && nums.at(0) != nums.at(1)) { return nums.at(0); }
    if(size >= 2 && nums.at(size - 1) != nums.at(size - 2)) { return nums.at(size - 1); }
    for(int i = 1; i < size - 1; ++i) {
        cout << i << endl;
        if(nums.at(i - 1) == nums.at(i) || nums.at(i) == nums.at(i + 1)) { continue; }
        else { return nums.at(i); }
    }
    return 0;
}
```

#### 其他方法

```cpp
    int singleNumber(vector<int>& nums) {
        int result = 0;
        for (int i = 0; i < 32; i++) {
            int mask = 1 << i;
            int cnt = 0;
            for (int j = 0; j < nums.size(); j++) { if ((nums[j] & mask) != 0) { cnt++; } }
            if (cnt % 3 != 0) { result |= mask; }
        }
        return result;
    }
```

### 第四题 [403. 青蛙过河](https://leetcode-cn.com/problems/frog-jump/)

难度：困难

类型：动态规划

#### 原题描述

一只青蛙想要过河。 假定河流被等分为若干个单元格，并且在每一个单元格内都有可能放有一块石子（也有可能没有）。 青蛙可以跳上石子，但是不可以跳入水中。

给你石子的位置列表 stones（用单元格序号 升序 表示）， 请判定青蛙能否成功过河（即能否在最后一步跳至最后一块石子上）。

开始时， 青蛙默认已站在第一块石子上，并可以假定它第一步只能跳跃一个单位（即只能从单元格 1 跳至单元格 2 ）。

如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、k 或 k + 1 个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。

#### 解题思路

- dps
- 记忆化搜索
- 动态规划

这个题解非常直观了：[【宫水三叶】一题四解 : 降低确定「记忆化容器大小」的思维难度 & 利用「对偶性质」构造有效状态值](https://leetcode-cn.com/problems/frog-jump/solution/gong-shui-san-xie-yi-ti-duo-jie-jiang-di-74fw/)

#### 解题代码

```cpp
    bool canCrossRec(vector<int>& stones, int currIndex, int lastJump) {
        int size = stones.size();
        if(currIndex == size - 1) { return true; }
        for(int i = currIndex + 1; i < size; ++i){
            if(stones.at(i) == stones.at(currIndex) + lastJump - 1) {
                if(canCrossRec(stones, i, lastJump - 1)) { return true; } }
            if(stones.at(i) == stones.at(currIndex) + lastJump) {
                if(canCrossRec(stones, i, lastJump)) { return true; } }
            if(stones.at(i) == stones.at(currIndex) + lastJump + 1) {
                if(canCrossRec(stones, i, lastJump + 1)) { return true; }
                break;
            }
        }
        return false;
    }
	bool canCross(vector<int>& stones) {
        if(stones.at(0) != 0 || stones.at(1) != 1) { return false; }
        return canCrossRec(stones, 1, 1);
    }
```

> 以上解法超出时间限制

```cpp
    bool canCrossRec(vector<int>& stones, int currIndex, int lastJump, vector<vector<int>> &data) {
        if(data.at(currIndex).at(lastJump) != -1) {
            return data.at(currIndex).at(lastJump);
        }
        int size = stones.size();
        if(currIndex == size - 1) { return true; }
        for(int i = currIndex + 1; i < size; ++i){
            if(stones.at(i) == stones.at(currIndex) + lastJump - 1) {
                if(canCrossRec(stones, i, lastJump - 1, data)) {
                    data.at(currIndex).at(lastJump) = 1;
                    return true; 
                }
            }
            if(stones.at(i) == stones.at(currIndex) + lastJump) {
                if(canCrossRec(stones, i, lastJump, data)) { 
                    data.at(currIndex).at(lastJump) = 1;
                    return true; 
                }
            }
            if(stones.at(i) == stones.at(currIndex) + lastJump + 1) {
                if(canCrossRec(stones, i, lastJump + 1, data)) { 
                    data.at(currIndex).at(lastJump) = 1;
                    return true; 
                }
                break;
            }
        }
        data.at(currIndex).at(lastJump) = 0;
        return false;
    }
	bool canCross(vector<int>& stones) {
        if(stones.at(0) != 0 || stones.at(1) != 1) { return false; }
        vector<vector<int>> data(stones.size(), vector<int>(stones.size(), -1));
        return canCrossRec(stones, 1, 1, data);
    }
```

#### 其他解法

```cpp
bool canCross(vector<int>& stones) {
    if(stones.at(1) != 1) { return false; }
    int size = stones.size();
    vector<vector<bool>> dp(size, vector<bool>(stones.size(), false));
    dp[1][1] = true;
    for(int i = 2; i < size; ++i) {
        for(int j = 1; j < i; ++j) {
            int k = stones[i] - stones[j];
            if(k <= j + 1){ dp.at(i).at(k) = dp.at(j).at(k - 1) || dp.at(j).at(k) || dp.at(j).at(k + 1); }
        }
    }
    for(auto item : dp.at(size - 1)) { if(item) { return true; } }
    return false;
    }
};
```

### 第五题 [690. 员工的重要性](https://leetcode-cn.com/problems/employee-importance/)

难度：简单

类型：dfs, bfs, hash

#### 原题描述

给定一个保存员工信息的数据结构，它包含了员工 唯一的 id ，重要度 和 直系下属的 id 。

比如，员工 1 是员工 2 的领导，员工 2 是员工 3 的领导。他们相应的重要度为 15 , 10 , 5 。那么员工 1 的数据结构是 [1, 15, [2]] ，员工 2的 数据结构是 [2, 10, [3]] ，员工 3 的数据结构是 [3, 5, []] 。注意虽然员工 3 也是员工 1 的一个下属，但是由于 并不是直系 下属，因此没有体现在员工 1 的数据结构中。

现在输入一个公司的所有员工信息，以及单个员工 id ，返回这个员工和他所有下属的重要度之和。

#### 解题思路

由于数组的下标和员工的id可能不同，因此需要一个 hash 来存放员工 id 对应数组的下标。然后 从 给定 id 开始进行dfs活bfs就可以了

#### 解题代码

```cpp
    int getImportance(vector<Employee*> employees, int id) {
        if(employees.empty()) { return -1; }
        unordered_map<int, int> id2index;
        int size = employees.size(), result = 0;
        for(int i = 0; i < size; ++i) { id2index[employees.at(i)->id] = i; }
        stack<int> idStack; idStack.push(id);
        vector<bool> visited(size, false);
        while(!idStack.empty()) {
            int currIndex = id2index[idStack.top()];
            visited.at(currIndex) = true;
            result += employees[currIndex]->importance;
            idStack.pop();
            for(auto person : employees[currIndex]->subordinates) {
                if(!visited.at(id2index[person])) { idStack.push(person); }
            }
        }
        return result;
    }
```

### 第七题 [554. 砖墙](https://leetcode-cn.com/problems/brick-wall/)

难度：中等

类型：hash

#### 原题描述

你的面前有一堵矩形的、由 n 行砖块组成的砖墙。这些砖块高度相同（也就是一个单位高）但是宽度不同。每一行砖块的宽度之和应该相等。

你现在要画一条 自顶向下 的、穿过 最少 砖块的垂线。如果你画的线只是从砖块的边缘经过，就不算穿过这块砖。你不能沿着墙的两个垂直边缘之一画线，这样显然是没有穿过一块砖的。

给你一个二维数组 wall ，该数组包含这堵墙的相关信息。其中，wall[i] 是一个代表从左至右每块砖的宽度的数组。你需要找出怎样画才能使这条线 穿过的砖块数量最少 ，并且返回 穿过的砖块数量 。

#### 解题思路

把每一行的缝隙对应的位置放进 hash ，然后记录这个位置出现缝隙的次数。出现次数最多的就是穿过砖块最少的那个位置。

#### 解题代码

```cpp
    int leastBricks(vector<vector<int>>& wall) {
        unordered_map<int, int> indexCount;
        int size = accumulate(wall.at(0).begin(), wall.at(0).end(), 0);
        for(auto line : wall) {
            int count = 0;
            for(auto length : line) {
                count += length;
                if(count == size) { continue; }
                ++indexCount[count];
            }
        }
        int result = 0;
        if(indexCount.empty()) { return wall.size(); }
        for(auto [index, count] : indexCount) {
            if(!result) { result = count; }
            if(result < count) { result = count; }
        }
        return wall.size() - result;
    }
```

