---

title: 3Sum With Multiplicity
last_updated: Apr 06, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: 3sum-with-multiplicity.html


---


## #. Problem

[Link](https://leetcode.com/problems/3sum-with-multiplicity/)



## 2. My approach

3 for loops for starter, and it shows TLE(Time Limit Error).
O(N^3) is 3000 * 3000 * 3000 so makes sense.

Tried with DP, dp\[i\]\[j\] as counting numbers of *i* from *j* to *N (3000)*.
Worked, but quite slow. 

```cpp
class Solution {
public:
    int threeSumMulti(vector<int>& arr, int target) {
        int n = arr.size();
        long long ans = 0;
        
        int dp[101][3001];
        memset(dp, 0, 101*3001* sizeof(int));
        
        for(int i = arr.size()-1; i >= 0; i--){
            for(int idx = 0; idx < 101; idx++){
                dp[idx][i] = dp[idx][i+1];
                if(idx == arr[i]) dp[idx][i] += 1;
            }
        }
        
        
        for(int i = 0; i < n-2; i++){
            for(int j = i+1; j < n-1; j++){
                int curTarget = target - (arr[i] + arr[j]);
                if(curTarget < 0 || curTarget > 100) continue;
                ans += dp[curTarget][j+1];
                ans %= (long long)1e9+7;
            }
        }
        
        return ans;
    }
};
```

| Time(O(?)) | Space(O(?)) |
| ---------- | ----------- |
| 441 ms     | 11.8 MB     |

## 2. Second approach - Map

On the description, it says:
> i, j, k such that i < j < k and arr[i] + arr[j] + arr[k] == target.

and this is literally the whole reason of struggles. 

It basically means, i != j != k. doesn't mean that you need to care about index order.
Thereby, \[1,2,3\] and \[3,2,1\] will show the same result through this algorithm.

With this idea, using unordered map with combination would solve the problem faster.

```c++
class Solution {
public:
    int threeSumMulti(vector<int>& arr, int target) {
        long long ans = 0;
        unordered_map<int, int> um;
        
        for(int el : arr){
            um[el]++;
        }
        
        for(auto el1 : um){
            for(auto el2 : um){
                int i = el1.first, j = el2.first, k = target-(i+j);
                
                if(!um.count(k)) continue;
                
                long long prevAns = ans;
                if(i < j && j < k) ans += (el1.second * el2.second * um[k]);
                // nCr = n!/(n-r)!r!
                
                // nC3 = (n * n-1 * n-2) / 3 * 2 
                if(i == j && j == k) ans += ((long long)el1.second * (el1.second-1) * (el1.second-2))/6;
                
                // nC2 * um[k] = (n * n-1) / 2 * um[k]
                if(i == j && j != k) ans += (el1.second * (el1.second - 1)) / 2 * um[k];
                if(!(ans - prevAns)) continue;
                // cout << i << '+' << j << '+' << k << " = " << ans - prevAns << endl; 
                ans %= (long long)1e9+7;
            }
        }
        
        
        return ans;
    }
};
```


| Time(O(N^2)) | Space(O(N)) |
| ---------- | ----------- |
| 12 ms      | 10.7 MB     |



## 3. Epilogue 

What I've learned from this exercise:

- wording is so important sometimes.
