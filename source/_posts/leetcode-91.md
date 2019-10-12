---
title: leetcode 91题
date: 2019-10-11 22:29:27
mathjax: true
tags:
- leetcode
- DP
---

解码方法，通过从尾部分析开始得到转移方程和边界条件。

<!--more-->

# 分析问题

首先这个问题的目标是求解有多少种解码方法，即给定的一串数字串有多少种解码方法。通俗一点理解这个目标就是，找到一个纯数字字符串有多少种拆分的方法。

那么根据编码规则我们可以得到以下规则

1. 对于1-9的数字，自然可以成为一个解码位
2. 对于0-6的数字，如果其前方数字为1-2那么这两个数字可以作为一个解码位

但是问题的关键问题是，如何确定是某个位置的数字是作为单一编码（1-9）还是组合编码（10-26）所以有
 
1. 当一个位置的字符是0，如果其前面数字为1，2，那么一定要和前面的数字共同进行组合成为一个解码位，其他情况直接报错
2. 当一个位置的字符是7-9时，那么其自然是一个单解码位
3. 当一个位置的字符是1-6时，如果前面的字符为1，2那么有两种方式 *选择DP的原因*，否则只有一种方式 

考虑边界条件

1. 字符串长度为0，返回0
2. 字符串首字母为0，返回0
3. 字符串中出现0且前面的数字不为1，2返回0

# 解决问题

在明确了使用DP解决问题之后，找到转移方程就可以做出来了。

假设解决问题的函数为  $F$ 字符串为 $S$ 则根据问题可以得到

1. 当 $i$ 位置的数字可以单独成为一个编码的时候，则 $F \left( i \right) = F \left( i-1 \right)$
2. 当 $i$ 位置的数字可以和前一个数字组成一个编码的时候 $F \left( i \right) = F \left( i-2 \right)$
3. 结合前两个条件，便可以得到，当 $i$ 位置的数字同时符合1、2条件的时候 $F \left( i \right) = F \left( i-1 \right) +  F \left( i-2 \right)$
4. 当 $i$ 的值为0时，那么如果不能和前一位数字组成编码，则不能解码返回0，否则 $F \left( i \right) = F \left( i-1 \right)$

## C++ 答案

```cpp
#include<string>
#include<vector>
using namespace std;
class Solution {
public:
    int numDecodings(string s) {
        int n=s.size();
        if(s.empty()) return 0;
        if(s[0]=='0') return 0;

        vector<int> dp(n+1,0); // 默认都是第四个条件

        dp[0]=1;
        dp[1]=1; // 如果只有一个字符且不为0则一定为1

        for(int i=2;i<n+1;++i)
        {
            if(s[i-1]!='0') dp[i]+=dp[i-1]; // 分析条件中的第一个条件命中

            if(s.substr(i - 2, 2) <= "26" && s.substr(i - 2, 2) >= "10") dp[i]+=dp[i-2]; // 分析条件中的第二个条件命中

            // 两个条件组合命中则为第三个条件命中
        } 
        return dp[n];
    }

};
```

## C++ 优化空间后的答案

优化思路，将第四个条件放入循环中进行判断？ TODO。。。

```cpp
#include<string>
#include<vector>
using namespace std;
class Solution {
public:
    int numDecodings(string s) {
        int n=s.size();
        if(s.empty()) return 0;
        if(s[0]=='0') return 0;

        vector<int> dp(n+1,0);

        dp[0]=1;
        dp[1]=1;

        for(int i=2;i<n+1;++i)
        {
            if(s[i-1]!='0') dp[i]+=dp[i-1];
            if(s.substr(i - 2, 2) <= "26" && s.substr(i - 2, 2) >= "10") dp[i]+=dp[i-2];
        }
        return dp[n];
    }

};
```
