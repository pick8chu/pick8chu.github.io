---

title: Delete and Earn
last_updated: Mar 06, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: delete-and-earn.html


---


## #. Problem

[Link](https://leetcode.com/problems/delete-and-earn/)



## 1. My approach

Just an easy DFS.
It gave the time limit error. 

```cpp
class Solution {
public:
    int score = 0;
    int arr[20002];
    
    int deleteAndEarn(vector<int>& nums) {
        unordered_set<int> s; 
        
        // for(int i = 0; i < 20002; i++){
        //     arr[i] = 0;
        // }
        
        for(auto& el : nums){
            arr[el]++;
            s.insert(el);
        }
        
        // for(auto& el: s){
            // cout << el << " ";
        dfs(s, 0);
        // }
        
        return score;
    }
    
    void dfs(unordered_set<int> s, int cur_score){
        bool flag = false;
        for(auto& el: s){
            // cout<<el<<endl;;
            int score_cnt = arr[el];
            if(score_cnt == 0) {
            // cout<<0<<endl;;
                continue;
            } else {
                flag = true;
                cur_score += el * score_cnt;
                int temp0 = arr[el-1];
                int temp1 = arr[el+1];
                int temp2 = arr[el];
                arr[el-1] = 0;
                arr[el+1] = 0;
                arr[el] = 0;
                // cout << el << "*" << score_cnt << " ";
                dfs(s, cur_score);
                // dfs(m, cur_score);
                cur_score -= el * score_cnt;
                arr[el-1] = temp0;
                arr[el+1] = temp1;
                arr[el] = temp2;
            }
        }
        
        if(!flag){
            // cout<<" done => "<< cur_score << endl;;
            score = max(score, cur_score);
        }
    }
};
```



## 2. Second approach - DP

Thinking it could be solved by DP, like stairs problem, I applied similar logic and it worked.

```c++
class Solution {
public:
    int arr[20002];
    int dp[20002];
    
    int deleteAndEarn(vector<int>& nums) {

        for(auto& el : nums){
            arr[el]++;
        }
        
        dp[1] = arr[1];
        for(int i = 2; i < 20001; i++){
            dp[i] = max(dp[i-2] + (i*arr[i]), dp[i-1]);
        }
        
        return dp[20000];
    }
};

```

Didn't need to go all the way to 20001, it's more natural to get max number and calculate till that. Also for the arrays, it could be substituted by vectors.

| Time(O(N)) | Space(O(N)) |
| ---------- | ----------- |
| 4 ms       | 9.2 MB      |



## 3. Epilogue 

What I've learned from this exercise:

- DP!
