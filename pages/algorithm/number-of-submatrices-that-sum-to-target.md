---
title: leetcode- Number of Submatrices That Sum to Target
last_updated: April 18, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: number-of-submatrices-that-sum-to-target.html
summary: map

---

## #. Problem

[Problem link](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/)



## 1. My approach 

I thought, it has to be DP, so I came out with a solution of 4d matrix, but I manage to make it into 2d matrix.

This one is with 4d matrix, very straight forward.
```c++
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        int ans = 0;
        int rowSize = matrix.size();
        int colSize = matrix[0].size();
        int vBox, hBox, dupBox;
        
        vector<vector<vector<vector<int>>>> dp(rowSize, vector<vector<vector<int>>>(colSize, vector<vector<int>>(rowSize, vector<int>(colSize, 0))));
        
        // vector<vector<bool>> flag(rowSize, vector<bool>(colSize, 0));
        
        for(int startRow = 0; startRow < rowSize; startRow++){
            for(int startCol = 0; startCol < colSize; startCol++){
                // fill(flag.begin(), flag.end(), vector<bool>(colSize, 0));
                // memset(flag, 0, sizeof(flag[0][0]) * rowSize * colSize);
                dp[startRow][startCol][startRow][startCol] = matrix[startRow][startCol];
                if(matrix[startRow][startCol] == target) ans++;
                
                for(int endRow = startRow; endRow < rowSize; endRow++){
                    for(int endCol = startCol; endCol < colSize; endCol++){
                        if(endRow == startRow && endCol == startCol) continue;
                        
                        vBox = endRow-1 >= startRow? dp[startRow][startCol][endRow-1][endCol] : 0;
                        hBox = endCol-1 >= startCol? dp[startRow][startCol][endRow][endCol-1] : 0;
                        dupBox = (endCol-1 >= startCol && endRow-1 >= startRow) ? dp[startRow][startCol][endRow-1][endCol-1] : 0;
                        
                        dp[startRow][startCol][endRow][endCol] = vBox + hBox - dupBox + matrix[endRow][endCol];
                        if(dp[startRow][startCol][endRow][endCol] == target) ans++;
                    }
                }
            }
        }
        
        // for(int i = 0; i < rowSize; i++){
        //     for(int j = 0; j < colSize; j++){
        //         cout<< dp[0][0][i][j] << ' ';
        //     }
        //     cout << endl;
        // }
        
        
        return ans;
    }
};
```

But it gives me a timeout. I assumed it's probably due to the vector size or initiation time.

Here is 2d matrix version;
```c++
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        int ans = 0;
        int rowSize = matrix.size();
        int colSize = matrix[0].size();
        int vBox, hBox, dupBox;
        
        vector<vector<int>> dp(rowSize, vector<int>(colSize, 0));

        dp[0][0] = matrix[0][0];
        if(dp[0][0] == target) ans++;
        for(int endRow = 0; endRow < rowSize; endRow++){
            for(int endCol = 0; endCol < colSize; endCol++){
                if(endRow == 0 && endCol == 0) continue;

                vBox = endRow-1 >= 0? dp[endRow-1][endCol] : 0;
                hBox = endCol-1 >= 0? dp[endRow][endCol-1] : 0;
                dupBox = (endCol-1 >= 0 && endRow-1 >= 0) ? dp[endRow-1][endCol-1] : 0;

                dp[endRow][endCol] = vBox + hBox - dupBox + matrix[endRow][endCol];
                if(dp[endRow][endCol] == target) ans++;
                
                for(int startRow = -1; startRow < endRow; startRow++){
                    for(int startCol = -1; startCol < endCol; startCol++){
                        if(startRow == -1 && startCol == -1) continue;
                        hBox = startRow == -1? 0 : dp[startRow][endCol];
                        vBox = startCol == -1? 0 : dp[endRow][startCol];
                        dupBox = startRow == -1 || startCol == -1? 0 : dp[startRow][startCol];
                        
                        if(dp[endRow][endCol] - (hBox + vBox - dupBox) == target){
                            ans++;
                        } 
                    }
                }
            }
        }
        
        return ans;
    }
};
```

| Time(N^2 * M^2) | Space(N*M) |
| --------------- | ---------- |
| 1844 ms         | 9.5 MB     |

I barely made it.


## 2. Solution

[Solution](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/discuss/1162767/JS-Python-Java-C%2B%2B-or-Short-Prefix-Sum-Solution-w-Explanation)

Apparently, it could be solved with the same logic from [this problem.](https://leetcode.com/problems/subarray-sum-equals-k/description/) 



### DP

```java
class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& M, int T) {
        int xlen = M[0].size(), ylen = M.size(), ans = 0;
        unordered_map<int, int> res;
        for (int i = 0; i < ylen; i++)
            for (int j = 1; j < xlen; j++)
                M[i][j] += M[i][j-1];
        for (int j = 0; j < xlen; j++)
            for (int k = j; k < xlen; k++) {
                res.clear();
				// why res[0] = 1 ?
                res[0] = 1;
                int csum = 0;
                for (int i = 0; i < ylen; i++) {
                    // M[i][k] = 0 ~ k
                    // M[i][j-1] = 0 ~ j-1 
                    // therefore, M[i][k] - M[i][j-1] => k ~ j-1
                    csum += M[i][k] - (j ? M[i][j-1] : 0);
                    ans += res.find(csum - T) != res.end() ? res[csum - T] : 0;
                    res[csum]++;
                }
            }
        return ans;
    }
};
```



I honestly cannot understand fully about this logic, but I kinda get it. I just don't understand why there's 'res[0] = 1' part... This logic is way faster than mine but took 10 times more of the space.

| Time(N^2 * M^2) | Space(N*M) |
| --------------- | ---------- |
| 484 ms          | 95.3 MB    |

## 3. Epilogue 

What I've learned from this exercise:

- Be smart and solve more problems.
