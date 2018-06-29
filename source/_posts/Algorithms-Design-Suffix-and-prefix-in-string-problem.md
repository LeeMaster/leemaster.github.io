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
