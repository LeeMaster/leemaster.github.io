---
title: LeetCode String problems
date: 2018-07-04 15:11:03
categories:
- Algorithms
tags:
- String
- LeetCode
---

# Algorithms

use leetcode to solve string algorithms

<!--more-->

# 6. ZigZag Conversion

Solution 

```java
class Solution {
    public String convert(String s, int numRows) {

        if (numRows == 1) return s;

        StringBuilder ret = new StringBuilder();
        int n = s.length();
        int cycleLen = 2 * numRows - 2;

        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j + i < n; j += cycleLen) {
                ret.append(s.charAt(j + i));
                if (i != 0 && i != numRows - 1 && j + cycleLen - i < n)
                    ret.append(s.charAt(j + cycleLen - i));
            }
        }
        return ret.toString();
    }
}
```

# 12. Integer to Roman

use a map to record all whole signs , and then use the reverse order to divide the origin numbers and then to append the sign then use loop to solve .

from the big to small , get the divede result .

Solution

```java
class Solution {
    public String intToRoman(int num) {
        Map<Integer,String> signTable = new HashMap<>();
        signTable.put(4,"IV");
        signTable.put(9,"IX");
        signTable.put(40,"XL");
        signTable.put(90,"XC");
        signTable.put(400,"CD");
        signTable.put(900,"CM");
        signTable.put(1，"I");
        signTable.put(5，"V");
        signTable.put(10,"X");
        signTable.put(50,"L");
        signTable.put(100,"C");
        signTable.put(500,"D");
        signTable.put(1000,"M");
        
        StringBuilder sb = new StringBuilder();
        
        List<Integer> integers = new ArrayList(signTable.keySet());
        Collections.sort(integers,Collections.reverseOrder());
        
        for(Integer i : integers){
            int less = num / i;
            if(less > 0){
                for(int j =0;j < less;j++){
                    sb.append(signTable.get(i));
                }
            }
            num = num - less * i;
        }
        
        return sb.toString();
        
    }
}
```

# 14. Longest Common Prefix

any string prefix will from the start to the end . the first will be common if not will return "" so as the next positions of the any strings

Solution

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null) return null;
        if (strs.length == 0) return "";

        Arrays.sort(strs); // use the dict order 
        char[] first = strs[0].toCharArray();
        char[] last  = strs[strs.length - 1].toCharArray();

        int i = 0, len = Math.min(first.length, last.length);
        while (i < len && first[i] == last[i]) i++;
        return strs[0].substring(0, i);
    }
}

```

# 71. Simplify Path

The path contain the case such as . and .. then can use the stack to resolve the problem ,and split will remove the duplicate /

Solution

```java
class Solution {
    public String simplifyPath(String path) {
		List<String> res = new ArrayList<>();
		String[] names = path.split("/");

		for (String str : names)
			if (str.length() != 0 && !str.equals("."))
				if (str.equals("..")) {
					if (res.size() > 0)
						res.remove(res.size() - 1);
				} else
					res.add(str);

		StringBuilder sb = new StringBuilder();

		for (String str : res)
			sb.append("/").append(str);

		return res.size() == 0 ? "/" : sb.toString();
	}
}
```


# 816. Ambiguous Coordinates

separation of the string is very usful for the string processing. this problem will separate the string to two segment and then handle the segment to add the decimal points and comma

Solution
