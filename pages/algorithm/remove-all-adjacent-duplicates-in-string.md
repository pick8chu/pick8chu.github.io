---
title: leetcode- Remove All Adjacent Duplicates In String
last_updated: Jun 29th, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: remove-all-adjacent-duplicates-in-string.html
summary: -

---

## #. Problem

[Link](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/)



## 1. My approach

### a. recursive

My initial thought was recursive. When I find 'two **adjacent** and **equal** letters,' I remove it right away.


```c++
class Solution {
public:
    string removeDuplicates(string s) {
        
        int num = 1;
        for(int i = 1; i <= s.length(); i++){
            if(num == 2){
                return removeDuplicates(s.substr(0, i-num) + s.substr(i));
            } else if(s[i-1] == s[i]){
                num++; 
            } 
        }
        
        return s;
    }
};
```



| Time(O(n*n)) | Space(n) |
| ------------ | -------- |
| 260 ms       | 432.6 MB |



### b. dequeue

and now I know all I need to see is last and current letters, stack seems like a good idea but to make a string, it doesn't seem to fit since it doesn't have iterations. So I used dequeue, and it made it way faster.


```c++
class Solution {
public:
    string removeDuplicates(string s) {
        deque<char> dq;
        string ans;
        
        for(auto el : s){
            if(!dq.empty() && dq.back() == el){
                dq.pop_back();
            } else {
                dq.push_back(el);
            }
        }
        
        while(!dq.empty()){
            ans += dq.front();
            dq.pop_front();
        }
        
        return ans;
    }
};
```



| Time(O(n)) | Space(n) |
| ---------- | -------- |
| 16 ms      | 10.8 MB  |



### c. vector

But when I think about it, if I use dequeue I have to pop front which will takes more time. So I changed it to vector, which didn't change much.


```c++
class Solution {
public:
    string removeDuplicates(string s) {
        vector<char> v;
        string ans;
        
        for(auto el : s){
            if(!v.empty() && v.back() == el){
                v.pop_back();
            } else {
                v.push_back(el);
            }
        }
        
        for(auto el : v){
            ans += el;
        }
        
        return ans;
    }
};
```



| Time(O(n)) | Space(n) |
| ---------- | -------- |
| 20 ms      | 11.3 MB  |



## 2. Best Practice

And I realized, I could just use string as stack, which makes me no need to run the last for loop.



```c++
class Solution {
public:
    string removeDuplicates(string s) {
        string ans;
        
        for(auto el : s){
            if(!ans.empty() && ans.back() == el){
                ans.pop_back();
            } else {
                ans.push_back(el);
            }
        }
        
        return ans;
    }
};
```

| Time(O(n)) | Space(n) |
| ---------- | -------- |
| 12 ms      | 10.2 MB  |



## 3. Epilogue 

What I've learned from this exercise:

- Remember many different structures have similar functions but it all works differently, time and space. 
