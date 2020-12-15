---
title: leetcode- Palindrome Partitioning
last_updated: Dec 15, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: palindrome_partitioning.html

---

## #. Problem

[Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)



## 1. My approach

I couldn't get how to partitioning it. 



## 2. DFS

Detail Explanations are here, [Solution page](https://leetcode.com/problems/palindrome-partitioning/solution/)

This is quite obvious solution if you think about it.



### First attempt

```c++
class Solution {
public:
    bool check(const string& s){
        for(int i = 0, j = s.length()-1; i < j; i++,j--){
            if(s[i] != s[j]) return false;
        }
        return true;
    }
    
    void dfs(const string& s, vector<string> v, vector<vector<string>>& ans){
        if(s.empty()) {
            ans.push_back(v);
            return;
        }
        
        for(int i = 1; i <= s.length(); i++){
            string tempStr = s.substr(0,i);
            
            if(!check(tempStr)) continue; 
            
            v.push_back(tempStr);
            dfs(s.substr(i), v, ans);
            v.pop_back();
        }
    }
    
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        dfs(s, vector<string>(), ans);
        return ans;
    }
};
```

| time   | space    |
| ------ | -------- |
| 300 ms | 136.9 MB |



### Optimized attempt

```c++
class Solution {
public:
    bool check(const string& s){
        for(int i = 0, j = s.length()-1; i < j; i++,j--){
            if(s[i] != s[j]) return false;
        }
        return true;
    }
    
    //  vector<string> v -> vector<string>& v : It'll pop back and give it as it were before when it comes back so pass by reference works, and better for space efficiency
    //  substr takes s.length time, so not using it and pass current index through parameter is more efficient.
    void dfs(const string& s, vector<string>& v, vector<vector<string>>& ans, int idx){
        if(idx == s.length()) {
            ans.push_back(v);
            return;
        }
    
        string cur;
        for(int i = idx; i < s.length(); i++){
            cur += s[i];
            if(!check(cur)) continue; 
            
            v.push_back(cur);
            dfs(s, v, ans, i + 1);
            v.pop_back();
        }
    }
    
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        vector<string> passbyReferenceIsOkay;
        dfs(s, passbyReferenceIsOkay, ans, 0);
        return ans;
    }
};
```

| time   | space   |
| ------ | ------- |
| 136 ms | 49.7 MB |




## 3. Epilogue 

What I've learned from this exercise:

- Passing by references could be very efficient, and mostly works in DFS.
