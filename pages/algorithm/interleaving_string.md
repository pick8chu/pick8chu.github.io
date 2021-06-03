---
title: leetcode- Interleaving String
last_updated: Jun 3rd, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: interleaving_string.html
summary: To compare my method and someone else's. 

---

## #. Problem

[Link](https://leetcode.com/problems/interleaving-string/)



## 1. My approach

Brute-force. 


```c++
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if(s1.length() + s2.length() != s3.length()) return false;
        /* this condition is on the problem desc, but somehow they have case against it.
         * so I removed it
         */
        // if(abs((int)(s1.length() - s2.length())) > 1) return false;
        
        return dfs(s1,s2,s3,0,0,0);
    }
    
    bool dfs(string &s1, string &s2, string &s3, int idx1, int idx2, int idx3){
        if(idx3 == s3.length()) return true;
        
        bool res = false;
        if(s3[idx3] == s1[idx1] && idx1 < s1.length()){
            res |= dfs(s1, s2, s3, idx1+1, idx2, idx3+1);
        }
        if(s3[idx3] == s2[idx2] && idx2 < s2.length()){
            res |= dfs(s1, s2, s3, idx1, idx2+1, idx3+1);
        }
        
        return res;
    }
};
```



This gave me time limit error. 



## 2. [Solutions](https://leetcode.com/problems/interleaving-string/solution/)

### a. Recursion with memoization

This, I couldn't come up with since I didn't think there's gonna be overlapping sub-problems. However, it apparently could have overlapping sub-problems. For example, there could be a situation that will have the same numbers in *idx1, idx2, idx3* respectively, although it reaches that states with different steps in the past.  



```c++
class Solution {
    
    int flag[101][101][201];
public:
    bool isInterleave(string s1, string s2, string s3) {
        if(s1.length() + s2.length() != s3.length()) return false;
        // if(abs((int)(s1.length() - s2.length())) > 1) return false;
        
        memset(flag, -1, sizeof(flag));
        return dfs(s1,s2,s3,0,0,0);
    }
    
    bool dfs(string &s1, string &s2, string &s3, int idx1, int idx2, int idx3){
        if(idx3 == s3.length()) return true;
        
        if(flag[idx1][idx2][idx3] != -1) return flag[idx1][idx2][idx3];
        
        bool res = false;
        if(s3[idx3] == s1[idx1] && idx1 < s1.length()){
            res |= dfs(s1, s2, s3, idx1+1, idx2, idx3+1);
        }
        if(s3[idx3] == s2[idx2] && idx2 < s2.length()){
            res |= dfs(s1, s2, s3, idx1, idx2+1, idx3+1);
        }
        
        return flag[idx1][idx2][idx3] = res;
    }
};
```



barely accepted with faster than 6.87%. Time complexity should be O(s1.length * s2.length) but it does seem quite slow. 

| Time  | Space   |
| ----- | ------- |
| 92 ms | 15.2 MB |



### b. Using 2D Dynamic Programming

This one seem to be really fast compare to a. solution. 

More information is [here](https://leetcode.com/problems/interleaving-string/discuss/1247165/C%2B%2B-Memoizn(3-variables)-greater-Memoizn(without-3rd-var)-greater-DP-(m*n)-greater-DP(n)), 3rd approach. 

Interestingly, the time complexity is the same with a. solution, but actual execution time is quite different. 



## 3. Epilogue 

What I've learned from this exercise:

- Need more practice on many different examples.
- DP is often the fastest solution even time complexity is the same.
