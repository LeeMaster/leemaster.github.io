---
title: leetcode 46题
date: 2019-12-16 00:39:51
mathjax: true
categories:
- Algorithm
tags:
- leetcode
---

一道可以把backtrack搞清楚的leetcode题目

<!--more-->

最近总在做一些DFS的题目，DFS本质是通过栈对整个状态空间进行枚举从而得到最优解的过程，DFS是对每一个阶段的状态进行试探，如果当前的状态被接受，而在同级的情况下有另外一个状态，那么需要在下次DFS的时候对邻居状态进行搜索。这个时候就会用到回溯的思想，当然剪枝的算法是为了减少DFS的搜索次数，因为可能到了某个状态后，已经不可能满足条件了，所以就不需要DFS了。

## 分析题目

这个题目还是比较简单的，直接寻找一个序列的全排列方案，注意下面两个点：

* 每一个元素只有被选择和不被选择两个状态
* C++ 中利用值传递保证子排列数组可以被拷贝

## C++ 答案

```cpp
#include <vector>

using namespace std;

class Solution {
public:
    vector<vector<int> > permute(vector<int>& nums) {
        vector<vector<int> > res;
        vector<int> track;
        backtrack(res, track, nums, 0);
        return res;
    }

    void backtrack(vector<vector<int> > & res, vector<int> vec, vector<int> & nums, int level){
        if(level == nums.size()){
            res.emplace_back(vec);
            return ;
        }
        for(auto i = 0; i < nums.size();i++){
            int j = 0;
            for(;j<vec.size();j++)
                if(nums[i]==vec[j]) break; 
            if(j!=vec.size()) continue; 
            // 添加状态
            vec.emplace_back(nums[i]);
            // 带着状态进行下一步搜索
            backtrack(res,vec,nums,level+1);
            // 移除状态，从而形成回溯
            vec.pop_back();
        }
    }
};
```

## 关于回溯的思考

首先回溯是搜索的一种特殊状态，很容易想到搜索，包括DFS和BFS，这两种搜索方式是根据上一步的状态，暴力地搜索下面的状态，而回溯是这两种搜索的扩展，因为有的问题需要带着每一步的状态直到最后的状态，所以搜索一个分支完成后，需要把这个分支搜索得到的状态删除了，才能进行另外一条分支的搜索。