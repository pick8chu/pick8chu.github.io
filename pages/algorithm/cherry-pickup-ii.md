
---
title: leetcode- Cherry Pickup II
last_updated: Jan 11, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: cherry-pickup-ii.html
summary: Matrix can be used not spacially but logically.

---



## #. Problem

[Link](https://leetcode.com/problems/cherry-pickup-ii/)



## 1. My approach

I couldn't come up with the solution. I was thinking dynamic programming with 2D matrix, but couldn't figure out where two robots collide. 


## 2. Solutions

> Hint1 : Use dynammic programming, define DP\[i\]\[j\]\[k\]: The maximum cherries that both robots can take starting on the *i* th row, and column *j* and *k* of Robot 1 and 2 respectively.

The key was not to think matrix as a spacial object but just a logical indicator of storage. (3D matrix is still imaginable but think it as a logical storage seems easier.)

```c++
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int rowSize = grid.size();
        int colSize = grid[0].size();
        // int dp[70][70][70];
        vector<vector<vector<int>>> dp(rowSize, vector<vector<int>>(colSize, vector<int>(colSize, 0)));
        
        for(int row = 0; row < rowSize; row++){
            for(int robot1 = 0; robot1 < colSize; robot1++){
                for(int robot2 = 0; robot2 < colSize; robot2++){
                    int curMax = 0;
                    if(row != 0) {
                        for(int i = -1; i < 2; i++){
                            for(int j = -1; j < 2; j++){
                                int curRobot1 = robot1+i;
                                int curRobot2 = robot2+j;
                                if(curRobot1 < 0 || curRobot1 >= colSize ||
                                    curRobot2 < 0 || curRobot2 >= colSize) continue;
                                curMax = max(dp[row-1][robot1+i][robot2+j], curMax);
                            }
                        }
                    }
                    //robot1 과 robot2가 가지 못하는 영역이 있기 때문
                    int robot1val = robot1 <= row ? grid[row][robot1] : 0;
                    int robot2val = robot2 >= colSize-1-row ? grid[row][robot2] : 0;
                    //robot1, robot2의 값이 같을 때는 하나만 먹을수 있기때문
                    curMax += robot1val + (robot1 != robot2? robot2val : 0);
                    dp[row][robot1][robot2] = curMax;
                }
            }
        }
        
        int ans = 0;
        // for(int row = 0; row < rowSize; row++){
            // cout << endl << row << endl;
            for(int robot1 = 0; robot1 < colSize; robot1++){
                for(int robot2 = 0; robot2 < colSize; robot2++){
                    ans = max(ans, dp[rowSize-1][robot1][robot2]);
                    // cout << robot1 << ", " << robot2 << ": " << dp[row][robot1][robot2] << endl;
                }
            }
        // }
        
        return ans;
    }
};
```


| Time(O(row\*col\*col)) | Space(O(row\*col\*col)) |
| ------------ | ------------- |
| 98 ms         | 14.4 MB          |



## 3. Epilogue 

What I've learned from this exercise:

- Matrix can be a storage (which it is) to be indicated with indices. 
  - ex) maxtrix\[1\]\[4\]\[2\]\[6\] : 1st floor's 4th room's 2nd desk's 6th chair's name (or something else).	 
