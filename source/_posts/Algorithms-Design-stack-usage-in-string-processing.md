---
title: Algorithms Design stack usage in string processing
date: 2018-07-01 02:09:57
categories:
- Algorithms
tags:
- String Problem
mathjax: true
---

# Stack 

The stack is a simple struct to use as FILO and we often do the string in processing may like to use one by one to handle the every character. So we can use the thought as stack or queue to handle the string 

<!--more-->

# When to choose the Stack to use

As we know that the string will be queued in a senquence order , and when the time we should cast the string to number or count the characters in string to use in next time , and then we can use the stack for example we could cast a string to an integer .

# examples 

## String to Integer (atoi)

> Implement atoi which converts a string to an integer.

> The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

> The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

> If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

> If no valid conversion could be performed, a zero value is returned.

> Note:
> * Only the space character ' ' is considered as whitespace character.
> * Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [$−2^{31}$,  $2^{31}$ − 1]. If the numerical value is out of the range of representable values, INT_MAX ($2^{31}$ − 1) or INT_MIN ($-2^{31}$) is returned.

```java
public static int myAtoi(String str) {

	String resString = str.trim();
	int range = resString.indexOf(' ');
	if (range != -1)
		resString = resString.substring(0, range);

	StringBuilder sb = new StringBuilder();
	char ch;
	for (int i = 0; i < resString.length(); i++) {
		ch = resString.charAt(i);
		if (i == 0 & (ch == '-' | ch == '+')) {
			sb.append(ch);
			continue;
		}
		if (!Character.isDigit(ch))
			break;
		sb.append(ch);
	}

	if (sb.length() == 0 | sb.toString().equalsIgnoreCase("-") | sb.toString().equalsIgnoreCase("+"))
		return 0;

	return (int) Double.parseDouble(sb.toString());

}
```

We don't use the stack struct instance to handle this problem but the thought of stack will be use in this solution code , we can infer that the string will start with - or non zero character will be legal and we just neeed the segment before the first withespace and then we could find the base solution as a stack we can use the stack to handle the every character and then will pop them in orider and then use a interation to increse the  cardinal number to add them to one integer .

## Rotate Function

> Given an array of integers A and let n to be its length.

> Assume Bk to be an array obtained by rotating the array A k positions clock-wise, we define a "rotation function" F on A as follow:

> F(k) = 0 * Bk[0] + 1 * Bk[1] + ... + (n-1) * Bk[n-1].

> Calculate the maximum value of F(0), F(1), ..., F(n-1).

formula handle method 

first get this problem I missed because of the clock-wise and after then I watch the clock then I find the solution , we can see the Bk arrary as the ring like the ring and the elemtns will be rotate in the clock-wise and then calculate the max use the function 

for example the first is **f(0) = 0 * Bk[0] + 1 * Bk[1] + ... + (n-1) * Bk[n-1].** and the next **f(1) = 0 * Bk[n-1] + 1 * Bk[1] + ... + (n-1) * Bk[n-2]** it is that the first element will be push to the next and the final element will be push to the first position and then use the function to calculate the result .

I can find the f(1) - f(0) as the f(k) - f(k-1) like this , $({n-1}) \times Bk \lbrack {n-1} \rbrack - \sum_{i=0}^{n-2} {Bk \lbrack {i} \rbrack}$ that will be $({n}) \times Bk \lbrack {n-1} \rbrack - \sum_{i=0}^{n-1} {Bk \lbrack {i} \rbrack}$

and then I can do as this firstly calculate the sum of the all senquence fo this array and then use the max value to record the max then to do so .

```java
int allSum = 0;
int len = A.length;
int F = 0;
for (int i = 0; i < len; i++) {
    F += i * A[i];
    allSum += A[i];
}
int max = F;
for (int i = len - 1; i >= 1; i--) {
    F = F + allSum - len * A[i];
    max = Math.max(F, max);
}
return max; 
```

