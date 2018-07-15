---
title: Algorithms Design Suffix and prefix in string problem
date: 2018-06-29 11:50:04
categories:
- Algorithms
tags:
- String Problem
mathjax: true
---

# What is the suffix and the prefix 

String puzzles are really difficult for some programmer to solve them . I think these puzzles will have the common characters , and the most common thing in the string puzzles are the suffix and the prefix .

<!--more-->

## Suffix Prefix usage 

They will be used in the judgement for the substring or the match puzzle , for example we can use DP to solve the LCS problem, the LCS common character is that , we should find a max length for a string which contain the most same characters in two strings .

We assume that $N$ and $M$ we want find the LCS in them as $S$ 
What we do is that we want find the common characters in the two strings we belive that when $N_i$ equals $M_j$ then we just need know that the $N_{i-1}$ equals $M_{j-1}$ or not, when they in equalment then we just need add the number in DP[i][j] = DP[i-1][j-1] + 1 , this method just used to solve the puzzle to find the max length between two strings to find the LCS, but how to display the LCS on the screen on the comman lines ? 

We should design a track algoritm to solve them in action 

![Track solution](http://ww1.sinaimg.cn/large/0079qc5pgy1fsrx95fxzvj30ae0aotad.jpg)

like the image show we show record the path from the first to the next state , that is the important to solve the DP problem . state to state , right ?

As we have solve the base situation then I will do the next thing to record the track path from the start to the endpoint , I think this thing essence in the LCS puzzle is that the suffix is only one character then we just should know whether the prefixs are same or not . and for the start state we should have two charactes to define the prefix and the suffix to use , like the we should start our iteration from the second charcter to the end and in this loop we can do many thing to use . and then we should have a flags array to record the path .


then the algorithm will be described as below .

1. define the inital string as the base state like two characters 
2. from the $M$ start to the end and inner loop is from $N$ start to then end 
3. then handle the suffix and the prefix 
4. traceback print the senquence to use 

we can write this code in Java

```java

public class Solution{
    
    public static void LCS(String m,String n){
        InnerCell[][] DP = new InnerCell[1010][1010];
        for(int i = 1;i < M.length();i++){
            for(int j = 1;j < N.length();j++){
                if(M.charAt(i-1) == N.charAt(j - 1)){
                    DP[i][j].nowLen = DP[i-1][j-1].nowLen + 1;
                    DP[i][j].from = Direction.TOPLEFT;
                }else if(DP[i-1][j].nowLen > DP[i][j-1].nowLen){
                    DP[i][j].nowLen = DP[i-1][j].nowLen;
                    DP[i][j].from = Direction.LEFT;
                }else{
                    DP[i][j].nowLen = DP[i][j-1].nowLen;
                    DP[i][j].from = Direction.TOP;
                }
            }
        }

        int m = M.length();
        int n = N.length();
        int k = 0;
        char c[1010];
        while(m > 0 && n > 0){
            if(DP[m][n].from == Direction.TOPLEFT){
                c[k++] = M.charAt(m-1);
                m--;n--;
            }else if(DP[m][n].from == Direction.TOP){
                m--;
            }else{
                n--;
            }
        }

        for(int cl = k-1; cl >= 0 ; cl--){
            System.out.print(c[cl]);
        }
        System.out.println();
    }

    public enum Direction{
        TOP,LEFT,TOPLEFT;
    }

    public static class InnerCell{
        public int nowLen = 0;

        public Direction from;
    }
}
```

The base absolusion do thin thing to use then we can record the common string in two senquence use dynamic programming.

In this example we can see the DP and the string puzzle essence is that we should handle the prefix and the suffix and handle the state , we can belive the string is construct by suffix and prefix then we can use this characte to construct a DP view to handle this program .

# examples 

## LPS longest palindromic substring

**Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.**

> Input: "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.

In this problem then we can see the character of a palindromic string in use ,we can belive that a palindromic is like this we assume that the $S$ is a string , but how do we judge wether the string is a palindromic string . It may do as this thing , we can see the $S$ length is $n$  and we could know that when the $S$ is a palindromic then we can find the truth that the $S_1$ equals the $S_n$ then we can use the concquer to solve this problem , as the basement of $S_1$ euals $S_n$  if the $S_2$ equals $S_{n-1}$ then we can belive the string is a palindromic when the string length is 4 . right  we can summarize  a recurrence relation to describe this puzzle 

if $S_i$ equals $S_j$ then we should judge the $S_{i+1}$ equals $S_{j-1}$ and we can know the origin problem is depended on the subproblem so we can get the relations:

> $S_{(i...n)}$ = $S_{(i+1...n-1)}$ $if$ $S_{i-1} = S_{n-1}$ 

And if not equalment then we can know that the max palindromic will in the $S_{i+1 ... n-1}$ right ?

the essence is that when we judge a string is a palindromic or not by the truth a suffix of a string is equals the prefix or not .

```java
class Solution {
    public String longestPalindrome(String s) {
      int n = s.length();
      String res = null;

      boolean[][] dp = new boolean[n][n];

      for (int i = n - 1; i >= 0; i--) {
        for (int j = i; j < n; j++) {
            // j == i or j - i == 2 then will be palindromic 
          dp[i][j] = s.charAt(i) == s.charAt(j) && (j - i < 3 || dp[i + 1][j - 1]);

          if (dp[i][j] && (res == null || j - i + 1 > res.length())) {
            res = s.substring(i, j + 1);
          }
        }
      }

      return res;
    }
}
```