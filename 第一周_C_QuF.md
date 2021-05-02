# 第一周_C_QuF

## I 本周目标

| 完成量 | 目标值 | 完成度 |
| :----: | :----: | :----: |
|   5    |   6    |        |

## II 本周刷题总结

### 第1题 [课程表](https://leetcode-cn.com/problems/course-schedule/description/)

难度：中等

类型：[`depth-first-search`](https://leetcode.com/tag/depth-first-search) | [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) | [`graph`](https://leetcode.com/tag/graph) | [`topological-sort`](https://leetcode.com/tag/topological-sort)

#### a. 原题陈述

你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi]` ，表示如果要学习课程 `ai` 则 **必须** 先学习课程 `bi` 。

- 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。

请你判断是否可能完成所有课程的学习？如果可以，返回 `true` ；否则，返回 `false` 。

#### b. 解题思路

这个题目就是判断一个图中是否有环，然后又是一个顺序的问题。于是想到了拓扑排序，当图中有环的时候，有些节点是无法通过拓扑排序来访问到的。因此记录拓扑排序访问到的节点数，再与总的节点数比较，不一样就说明有环（无法完成）

> 拓扑排序：用于解决有先后顺序的问题。依次访问入度为0的点，然后删掉这些点，就会又出现另外的入度为0的点，一直访问下去，就会得到一个序列，这个序列就是有先后顺序的序列

#### c. 解题代码

```cpp
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        vector<int> indegree(numCourses, 0);
        for (auto &line : prerequisites){ 
            graph[line.at(1)].push_back(line.at(0));
            ++indegree.at(line.at(0));
        } // 建图
        queue<int> visit;
        int count = 0;
        for (int i = 0; i < numCourses; ++i) { if (!indegree[i]) { visit.push(i); } 	}	// 找出入度为0的点
        while (!visit.empty()) {
            ++count;
            int currIndex = visit.front();
            visit.pop();
            for (auto &connectIndex : graph.at(currIndex)) {
                if (!--indegree.at(connectIndex)) {
                    visit.push(connectIndex);
                }	// 更新入度，并进行判断
            }
        }
        return count < numCourses ? false : true;
    }
```

#### d. 其他解法摘录

其他的包括DFS和BFS都会的兼容性

------

### 第2题 [课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/description/)

难度：中等

类型：[`depth-first-search`](https://leetcode.com/tag/depth-first-search) | [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) | [`graph`](https://leetcode.com/tag/graph) | [`topological-sort`](https://leetcode.com/tag/topological-sort)

#### a. 原题陈述

和上一题差不多，只不过返回值不一样。

返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回一种就可以了。如果不可能完成所有课程，返回一个空数组。

#### b. 解题思路

和上一题一样

#### c. 解题代码

```cpp
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph(numCourses);
        vector<int> indegree(numCourses, 0);
        for (auto &line : prerequisites) {
            graph.at(line.at(1)).push_back(line.at(0));
            ++indegree.at(line.at(0));
        }
        queue<int> temp;
        vector<int> result;
        int count = 0;
        for (int i = 0; i < numCourses; ++i) {
            if (!indegree.at(i)) { temp.push(i); result.push_back(i); }
        }
        while (!temp.empty()) {
            int currIndex = temp.front();
            ++count;
            temp.pop();
            for (auto &vec : graph.at(currIndex)) {
                if (--indegree.at(vec) == 0) {
                    temp.push(vec);
                    result.push_back(vec);
                }
            }
        }
        return count == numCourses ? result : vector<int>();
    }
```

#### d. 其他解法摘录

------

### 第3题 [实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/description/)

难度：中等

类型：[`design`](https://leetcode.com/tag/design) | [`trie`](https://leetcode.com/tag/trie)

#### a. 原题陈述

**[Trie](https://baike.baidu.com/item/字典树/9825209?fr=aladdin)**（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

- `Trie()` 初始化前缀树对象。
- `void insert(String word)` 向前缀树中插入字符串 `word` 。
- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false` 。
- `boolean startsWith(String prefix)` 如果之前已经插入的字符串 `word` 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。

#### b. 解题思路

前缀树（是一颗多叉树）的节点由字符组成，每个节点还由一个指向下面节点的指针，然后还得添加一个 string 类型成员变量来存放字符整体（这样你输出整个字符的化就会节省不少时间，空间换时间）

- 插入：一个字符一个字符的构建树
- 查找前缀：

#### c. 解题代码

```cpp
struct TrieNode {
    char value;
    unordered_map<char, TrieNode *> child;
    bool end = false;
    TrieNode() { }
    TrieNode(char data) { value = data; }
};
class Trie {
public:
    Trie() { root = new TrieNode(); }
    void insert(string word) {
        TrieNode *parent = root;
        for(auto &charItem : word) {
            TrieNode *tempNodeDel = new TrieNode(charItem);
            TrieNode *tempNode = tempNodeDel;
            auto pairs = parent->child.insert({charItem, tempNode});
            parent = pairs.first->second;
        }
        parent->end = true;
    }
    bool search(string word) {
        TrieNode *parent = root;
        for(auto &charItem : word) {
            auto iter = parent->child.find(charItem);
            if ( iter == parent->child.end() ) { return false; }
            else { parent = parent->child[charItem]; }
        }
        if (parent->end) { return true; }
        return false;
    }
    bool startsWith(string prefix) {
        TrieNode *parent = root;
        for(auto &charItem : prefix) {
            auto iter = parent->child.find(charItem);
            if ( iter == parent->child.end() ) { return false; }
            else { parent = parent->child[charItem]; }
        }
        return true;
    }
private:
    TrieNode* root;
};
```

#### d. 其他解法摘录

上面这种是比较通用的，但是时间和空间开销比较大。下面的解法把不需要建立一个节点类，十分的巧妙。

```cpp
class Trie {
private:
    int isEnd = false;
    array<Trie *, 26> child;	// 由于只有小写数字，因此用了数组，正常用 hash 比较好
public:
    Trie() {
        for (auto iter = child.begin(); iter != child.end(); ++iter) { *iter = nullptr; }
    }
    void insert(string word) {
        Trie *curr = this;
        for (auto &charItem : word) {
            int index = charItem - 'a';	
            if (!curr->child.at(index)) { curr->child.at(index) = new Trie(); }
            curr = curr->child.at(index);
        }
        curr->isEnd = true;
    }
    bool search(string word) {
        Trie * curr = this;	// 这里非常巧妙，不用动根节点
        for (auto &charItem : word) {
            int index = charItem - 'a';
            if (!curr->child.at(index)) { return false; }
            curr = curr->child.at(index);
        }
        return curr->isEnd;
    }
    bool startsWith(string prefix) {
        Trie * curr = this;
        for (auto &charItem : prefix) {
            int index = charItem - 'a';
            if (!curr->child.at(index)) { return false; }
            curr = curr->child.at(index);
        }
        return true;
    }
};
```

------

### 第4题 [词典中最长的单词](https://leetcode-cn.com/problems/longest-word-in-dictionary/description/)

难度：简单（我可感觉不简单）

类型：[`hash-table`](https://leetcode.com/tag/hash-table) | [`trie`](https://leetcode.com/tag/trie)

#### a. 原题陈述

给出一个字符串数组`words`组成的一本英语词典。从中找出最长的一个单词，该单词是由`words`词典中其他单词逐步添加一个字母组成。若其中有多个可行的答案，则返回答案中字典序最小的单词。

若无答案，则返回空字符串。

#### b. 解题思路

- 排序 + hash：用 `set<string>` 来储存数据，前缀出现再hash表中再把它塞进hash表中。用一个string来记录最长的且字典序最小的。排序为了保证字典序以及前缀出现在前面。这一种相对来说比较好想。
- trie + dfs：把数据重组成前缀树，然后通过dfs来遍历找到最大的

#### c. 解题代码

排序 + hash

```cpp
    string longestWord(vector<string>& words) {
        string result;
        unordered_set<string> data;
        sort(words.begin(), words.end());
        for (auto &word : words) {
            int size = word.size();
            if (size == 1) { 
                data.insert(word); 
                if (result.size() < word.size()) { result = word; }
            }
            else if (data.find(word.substr(0, size - 1)) != data.end()) {
                data.insert(word);
                if (result.size() < word.size()) { result = word; }
            }   
        }
        return result;
    }
```

trie + dfs

```cpp
struct Trie{
    array<Trie *, 26> child;
    string fullString;
    Trie(){ child.fill(nullptr); }
    void insert(string word) {
        Trie *curr = this;
        for (auto &c : word) {
            int index = c - 'a';
            if (curr->child.at(index) == nullptr) { curr->child.at(index) = new Trie(); }
            curr = curr->child.at(index);
        }
        curr->fullString = word;
    }	// 上面的部分和上一题差不多
    string dfs(){
        string result;
        stack<Trie *> trieStack;
        trieStack.push(this);
        while (!trieStack.empty()) {
            Trie *temp = trieStack.top();
            trieStack.pop();
            for(auto &item : temp->child) {
                if (item == nullptr) { continue; }
                string tempString = item->fullString;
                if(!tempString.empty()) {
                    trieStack.push(item);
                    if(result.size() < tempString.size() || 
                       (result.size() == tempString.size() && tempString < result)) {
                        result = tempString;
                    }
                }
            }
        }
        return result;
    }
};
class Solution {
public:
    string longestWord(vector<string>& words) {
        Trie trie;
        for (auto &word : words) { trie.insert(word); }
        return trie.dfs();
    }
};
```

#### d. 其他解法摘录

一般就这两种了

------

### 第6题 [数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

难度：简单

类型：数组、哈希表

#### a. 原题陈述

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

#### b. 解题思路

- 把数组排序
- hash

#### c. 解题代码

hash

```cpp
	int findRepeatNumber(vector<int>& nums) {
        unordered_set<int> data;
        for(auto &num : nums) { if(!data.insert(num).second) { return num; } }
        return -1;
    }
```

#### d. 其他解法摘录

hash 有 $O(n)$ 的空间复杂度。这个题目比较特殊，因此可以优化。

可以发现数组的范围是 $[0,n-1]$ 如果没有重复就可以一一对应与每个坐标，因此遍历数组，把他们交换到对应的坐标上，如果发现交换的两个元素相同，就找到了重复的元素，这里可以把空间复杂度降到 $O(1)$

```cpp
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        int size = nums.size();
        for(int i = 0; i < size; ++i) {
            while(nums[i] != i) {
                int temp = nums[i];
                if(temp == nums[temp]) { return temp; }
                nums[i] = nums[temp];
                nums[temp] = temp;
            }
        }
        return -1;
    }
};
```

如果加上一个不更改数组的要求，题目就会变的比较难（哈希也可以用，但是还是空间上的消耗比较大）

这时候就是使用二分算法了。这里的二分是重复数字范围的二分

```cpp
    int findRepeatNumber(vector<int>& nums) {
        int begin = 0, end = nums.size();
        int size = end;
        while(begin + 1 != end){
            int mid = (begin + end) >> 1, count = 0;
            for(int i = 0; i < size; ++i){
                if(begin <= nums[i] && nums[i] < mid) { ++count; }
            }
            cout << begin << " " << end << endl;
            if(mid < begin + count) { end = mid; }
            else { begin = mid; }
        }
        return begin;
    }
```

> 这个没通过leetcode，主要是leetcode的例子和题意不符
>
> 输入：[0, 1, 2, 0, 4, 5, 6, 7, 8, 9]  这里数组长度为9，数组中元素的范围应该是0-8，而题目中出现了9

