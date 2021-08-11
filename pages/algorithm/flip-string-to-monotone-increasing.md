---
title: leetcode- Flip String to Monotone Increasing
last_updated: Aug 11th, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: flip-string-to-monotone-increasing.html
summary: Very smart DP

---

## #. Problem

[Link](https://leetcode.com/problems/flip-string-to-monotone-increasing/)



## 1. My approach

Brute-force. Quite straight forward BFS.

My basic approach was, to see every possible situation, and see what would fit monotone the first.  So I change 0 to 1 starting from left to right, and change 1 to 0 starting from right to left, put them in a queue, continued it till I find the monotone string.


```c++
class State {
    public:
    string cur_str;
    int cur_cnt;
    int l;
    int r;
    
    State(string _cur_s, int _cur_cnt, int _l, int _r):cur_str(_cur_s), cur_cnt(_cur_cnt), l(_l), r(_r){}
    State(){}
};

class Solution {
public:
    bool is_monotone(string s){
        bool flag = false;
        char prev_ch = '0';
        for(int l = 0; l < s.size(); l++){
            if(prev_ch != s[l]){
                if(flag){
                    return false;
                } else {
                    prev_ch = s[l];
                    flag = true;   
                }
            }
        }

        return true;
    }

    int minFlipsMonoIncr(string s) {
        
        queue<State> q;
        
        int ans = 1000000;
        State end_state;
        q.push(State(s, 0, 0, s.size()-1));
        
        while(!q.empty() && ans == 1000000){
            auto el = q.front();
            string cur_str = el.cur_str;
            int cur_cnt = el.cur_cnt;
            int cur_l = el.l;
            int cur_r = el.r;
            q.pop();
            
            if(is_monotone(cur_str)) {
                ans = cur_cnt;
                end_state = el;
            }
            
            for(int r = cur_r; r >= 0; r--){
                if(cur_str[r] != 1){
                    string temp = cur_str;
                    temp[r] = '1';
                    
                    q.push(State(temp, cur_cnt+1, cur_l, r));
                    break;
                }
            }
            
            for(int l = cur_l; l < cur_str.size(); l++){
                if(cur_str[l] != 0){
                    string temp = cur_str;
                    temp[l] = '0';
                    
                    q.push(State(temp, cur_cnt+1, l, cur_r));
                    break;
                }
            }
        }
        
        return ans;
    }
};
```



This gave me time limit. (where 'n' is length of string s)

| Time(O(2^n)) | Space(O(2^n)) |
| ------------ | ------------- |
| ? ms         | ? MB          |



## 2. [Solutions](https://www.youtube.com/watch?v=7okODxrtO7A&t=304s&ab_channel=CodeWithSunny)

It's DP, starting with the idea that there will be only 3 monotone state.

0s, 1s, and 0s1s.

Therefore, we just need to see those 3 possible situations.

For example, when s = '00100011', there are 3 possible acts,

1. changing every 1 to 0 : 3 flips (since there are 3 ones)
2. changing every 0 to 1 : 5 flips (since there are 5 zeros)
3. 0s1s. For this, we have to check iteratively. When '*i*' is *0* to *n* iteratively, we use it as a separator, and consider that all the left elements from '*i*' will be 0 and all the right will be 1.

We have to decide which would be the minimum flip among 1, 2, and 3.



```c++
class Solution {
public:
    int minFlipsMonoIncr(string s) {
        int one_cnt = 0;
        int n = s.size();
        
        for(auto c : s) {
            one_cnt = c == '1'? one_cnt+1:one_cnt;
        }
        
        //	it's either 0 or 1
        int zero_cnt = n-one_cnt;
        int ans = min(one_cnt, zero_cnt);
        int cur_one_cnt = 0;
        
        for(int i = 0; i < n; i++){
            if(s[i] == '1') cur_one_cnt++;
            
            /**
            'cur_one_cnt' would be flip cost of changing 1 to 0 
            in every elements on left of 'i',
            '((n - 1) - i - (one_cnt - cur_one_cnt))' would be flip cost of
            changing 0 to 1 in every elements on right of 'i'.
            '(n - 1) - i' is number of right elements,
            '(one_cnt - cur_one_cnt)' is number of 0s on right elements.
            **/
            ans = min(ans, cur_one_cnt + ((n - 1) - i - (one_cnt - cur_one_cnt)));
        }
        
        return ans;
    }
};
```



| Time(O(n)) | Space(O(1)) |
| ---------- | ----------- |
| 32 ms      | 11 MB       |



## 3. Epilogue 

What I've learned from this exercise:

- You just have to be smart 😕 (it takes some times for me to understand the concept)
