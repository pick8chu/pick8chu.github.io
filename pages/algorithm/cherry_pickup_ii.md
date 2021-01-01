---
title: leetcode- Cherry Pickup II
last_updated: Dec 20, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: cherry_pickup_ii.html

---

## #. Problem

[Cherry Pickup II](https://leetcode.com/problems/cherry-pickup-ii/)



## 1. My approach

I used DFS, without knowing the time complexity.

### DFS

Obviously you cannot just grab what's good at the moment cause often time it might not be a right choice in the end. Thus, I figured it need to be searched completly(brute force), I coded follows, and turned out it was too slow.

```c++
class Solution {
public:
    int cherryPick(const pair<int,int>& r1, const pair<int,int>& r2, vector<vector<int>> map, int curAns){
        if(r1.first == map.size()){
            // cout<<" done";
            return curAns;
        }
        
        int r1_row = r1.first;
        int r1_col = r1.second;
        int r2_row = r2.first;
        int r2_col = r2.second;
        int temp1, temp2;
        int tempAns = 0;
        for(int i = -1; i < 2; i++){
            if(r1_col+i < 0 || r1_col+i > map[0].size()-1)
                continue;
            
            for(int j = -1; j < 2; j++){
                if(r2_col+j < 0 || r2_col+j > map[0].size()-1)
                    continue;
                
                temp1 = map[r1_row][r1_col+i];
                map[r1_row][r1_col+i] = 0;
                temp2 = map[r2_row][r2_col+j];
                map[r2_row][r2_col+j] = 0;
                
                tempAns = max(tempAns, cherryPick(pair<int,int>(r1_row+1, r1_col+i), pair<int,int>(r2_row+1, r2_col+j), map, curAns + temp1 + temp2));
                
                map[r1_row][r1_col+i] = temp1;
                if(r2_col+j != r1_col+i) map[r2_row][r2_col+j] = temp2;
            }
        }
        
        return tempAns;
    }
    
    int cherryPickup(vector<vector<int>>& grid) {
        return cherryPick(pair<int,int>(1,0), pair<int,int>(1,grid[0].size()-1), grid, 0) + grid[0][0] + grid[0][grid[0].size()-1];
    }
};
```

I didn't calculated the time, thinking this should be the only way, but you're supposed to use DP with this problem. 

Before I moved to DP, I tried to see the time complexity of DFS, and it's 3^row, which would be 3^70. No wonder it didn't work.

> every row it needs to do 3 steps after the previous stage respectively, which would make 3^N. It took quite a while for me to figure this out...




## 2. DP

```c++

```

| time | space |
| ---- | ----- |
| ms   | MB    |




## 3. Epilogue 

What I've learned from this exercise:

- 
