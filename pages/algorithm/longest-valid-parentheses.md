---
title: leetcode- Longest Valid Parentheses
last_updated: April 05, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: longest-valid-parentheses.html
summary: map

---

## #. Problem

[Problem link](https://leetcode.com/problems/longest-valid-parentheses)



## 1. My approach 

I was thinking of using a recursive algorithm. Like, if you find open parenthesis than recursively call again till it all popped up. Like:

(    

​	↘

​			(

​			)

​	↙

)



But it didn't work out well. I guess I was confused at some point and massed up little bit. So the problem was, '(()' is considered  2, but '()(()' this is also considered as 2. In my algorithm, since (() is considered 2, () + (() is considered to 4.

```c++
class Solution {
    bool flag = false;
public:
    int longestValidParentheses(string s) {
        pair<int,int> res;
        int ans = 0;
        int temp = 0;
        for(int i = 0; i < s.size(); i++){
            // cout << i << endl;
            if(s[i] == '('){
                // cout << '\t' << i << endl;
                res = open(s, i);
                i = res.first;
                if(flag) {   
                    flag = false;
                    continue;
                }
                temp += res.second;
                // cout << "temp : " << temp << endl;
                ans = max(ans, temp);
            } 
            else {
                temp = 0;
            }
        }
        
        return ans;
    }
    
    pair<int,int> open(const string& s, int idx){
        idx++;
        pair<int,int> res = make_pair(idx, 0);
        int temp = 0;
        
        for(int i = idx; i < s.size(); i++){
            cout<< i << endl;
            
            if(s[i] == '('){
                res = open(s, i);
                i = res.first;
                cout<< '\t' << i << endl;;
                temp += res.second;
                cout<< '\t' << temp << endl;
            } else {
                temp += 2;
                res.second = temp;                
                cout<< "done" << endl;
                return res;
            }
            
        }
        flag = true;
        res.second = temp;          
        return res;
    }
};
```



## 2. Solution

[Solution](https://leetcode.com/problems/longest-valid-parentheses/solution/)



They have 3 different  solutions (which I was very surprised, I struggled to even come out one solution), and DP approach was the most impressive one. They have one with stack, but i assume it's similar with the DP.



### DP

```java
public class Solution {
    public int longestValidParentheses(String s) {
        int maxans = 0;
        int dp[] = new int[s.length()];
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == ')') {
                if (s.charAt(i - 1) == '(') {
                    dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
                } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
                    dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
                }
                maxans = Math.max(maxans, dp[i]);
            }
        }
        return maxans;
    }
}
```



I highly suggest to look at all solutions when you have time, and at least think about it so that you could remember when you have to.



## 3. Epilogue 

What I've learned from this exercise:

- Be smart? or more to concentrate since I couldn't really concentrate fully.
