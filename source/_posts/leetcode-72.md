---
title: leetcode 72题
date: 2019-10-09 21:43:26
mathjax: true
tags:
- leetcode
- DP
---

字符串编辑距离

<!--more-->

# 分析问题

首先看到题目，明确目标，确定最小的编辑距离。

再得知对两个字符串的处理有三种方式：

1. 插入一个字符
2. 删除一个字符
3. 替换一个字符

那么假设有两个字符串 $S_1$ 和 $S_2$ 

可以做出如下推导，对于 $S_1$ 和 $S_2$ 可以有 $S_1 \left[ 0 ... i \right]$ 和 $S_2 \left[ 0 ... j \right]$ 两个子串

那么两个子串的编辑距离可以从其子问题得到解决即 $S_1 \left[ 0 ... i-1 \right]$ 和 $S_2 \left[ 0 ... j-1 \right]$ 的编辑距离

二者的关系是，字符串的编辑距离等于其子串编辑距离和两个子串下一个字符的比较结果得到，即

$S_1 \left[ 0 ... i \right]$ 和 $S_2 \left[ 0 ... j \right]$ 的编辑距离为

$S_1 \left[ 0 ... i-1 \right]$ 和 $S_2 \left[ 0 ... j-1 \right]$ 的结果为 $L$ 那么

对于 $S_1 \left[ i \right]$ 和 $S_2 \left[ j \right]$ 有两种情况 

1. 两个字符相等，则 $S_1 \left[ 0 ... i \right]$ 和 $S_2 \left[ 0 ... j \right]$ 的编辑距离为 $L$
2. 两个字符不想等，则有 $S_1 \left[ 0 ... i \right]$ 和 $S_2 \left[ 0 ... j \right]$ 的编辑距离为 $min \left( L ,S , R \right)$ 其中 $S$ 为 $S_1 \left[ 0 ... i-1 \right]$ 进行三种操作的结果 + 1，同理可知 $R$


# 解决问题

通过分析我们发现了，这个题目下，有重叠子问题，那么可以使用动态规划来解决。

首先考虑初始条件，初始化的条件就是假设每个字符串都有长度为0的可能性，那么当其中任意一个字符串长度为0的话，另外一个字符串的编辑距离是随着长度递增的。

## C++ 答案

```cpp
#include<vector>
using namespace std;
class Solution {
public:
    int minDistance(string word1, string word2) {
        int len1 = word1.size();
        int len2 = word2.size();
        if(len1 == 0)return len2;
        if(len2 == 0)return len1;
        vector<vector<int>> dp(len1,vector<int>(len2));
        // 初始化
        for(int i = 0;i < len1;i++)dp[i][0] = i;
        for(int i = 0;i < len2;i++)dp[0][i] = i;

        for(int i = 1;i < len1;i++){
          for(int j = 1;j < len2;j++){
            // 如果相等则编辑距离不变，如果不想等则三种操作选取最小的
            if(word1[i] == word2[j])dp[i][j] = dp[i-1][j-1];
            elsedp[i][j]=min(dp[i-1][j-1],min(dp[i][j-1],dp[i-1][j])+1);
          }
        }

        return dp[len1-1][len2-1];
    }
};
```

## Golang 答案

```golang
func minDistance(word1 string, word2 string) int {
    len1 := len(word1)
    len2 := len(word2)

    if len1 == 0 {
      return len2
    }

    if len2 == 0 {
      return len1
    }

    dp := [len1][len2]int

}
```

## Rust 答案

```rust
impl Solution {
    pub fn min_distance(word1: String, word2: String) -> i32 {
        
    }
}
```