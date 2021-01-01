---
title: leetcode- Coin Change
last_updated: Dec 13, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: coin_change.html

---

## #. Problem

[Coin Change](https://leetcode.com/problems/coin-change/)



## 1. My approach

### Reducing from bigger coins

So, it turned out this won't work if the coin is not small enough.
For examples, if the coins are [123, 653, 143], you can't be sure if the smallest one would fill the last.

### Brute Force

Time limits error. This one's little weird, I used DFS, and also cut it from bigger coins and if it found an answer then returns it. 
But it didn't work in some cases.

```c++
class Solution {
    int ans;    
public:
    
    void dfs(const vector<int>& coins, const int& cur_amount, const int& cur_idx, const int& cur_coins){
        if(ans != INT_MAX || cur_idx >= coins.size()) return;
        
        int temp_amount = cur_amount % coins[cur_idx];
        int coin_num = cur_amount / coins[cur_idx];
        if(temp_amount == 0) {
            // cout<< "!!!!" <<cur_coins + (coin_num) << '\n';
            ans = min(ans, cur_coins + (coin_num));
            return;
        }
        
        for(int i = 0; i <= coin_num; i++){
            // cout<< coins[cur_idx] << ':' << coin_num - i << '\n';
            dfs(coins, temp_amount + coins[cur_idx] * i, cur_idx+1, cur_coins + coin_num - i);
        }
    }
    
    int coinChange(vector<int>& coins, int amount) {
        ans = INT_MAX;
        
        sort(coins.begin(), coins.end(), [](const int& a, const int& b){
            return a > b;
        });
        
        dfs(coins, amount, 0, 0);
        
        return ans == INT_MAX? -1:ans;
    }
};
```


| Input:           | Output: | Expected: |
| ---------------- | ------- | --------- |
| [186,419,83,408] | 26      | 20        |



So, I tried with full brute force and of course it gives an time limits error.

```c++
class Solution {
    int ans;    
public:
    
    void dfs(const vector<int>& coins, const int& cur_amount, const int& cur_idx, const int& cur_coins){
        if(cur_idx >= coins.size()) return;
        
        int temp_amount = cur_amount % coins[cur_idx];
        int coin_num = cur_amount / coins[cur_idx];
        if(temp_amount == 0) {
            // cout<< "!!!!" <<cur_amount << ':' << cur_idx << ':' << cur_coins << ':' << (coin_num) << '\n';
            ans = min(ans, cur_coins + (coin_num));
            return;
        }
        
        for(int i = 0; i <= coin_num; i++){
            // cout<< coins[cur_idx] << ':' << coin_num - i << '\n';
            dfs(coins, temp_amount + coins[cur_idx] * i, cur_idx+1, cur_coins + coin_num - i);
        }
    }
    
    int coinChange(vector<int>& coins, int amount) {
        ans = INT_MAX;
        
        sort(coins.begin(), coins.end(), [](const int& a, const int& b){
            return a > b;
        });
        
        dfs(coins, amount, 0, 0);
        
        return ans == INT_MAX? -1:ans;
    }
};
```





## 2. DP

Detail Explanations are belows:

1. [Solution page](https://leetcode.com/problems/coin-change/solution/)
2. [Explanation with video](https://www.youtube.com/watch?v=jgiZlGzXMBw&ab_channel=BackToBackSWE)

Just to summarize, it has duplicated sub-problems which you could just refer from table whenever it shows. 



### w/ DFS (recursive)

```c++
class Solution {
    
public:
    int coinChange(const vector<int>& coins, const int& amount, vector<int>& v){
        if(amount < 0) return -1;
        if(amount == 0) return 0;
        if(v[amount] != 0) return v[amount];
        
        int dif, res, minI = INT_MAX;
        for(int i = 0; i < coins.size(); i++){
            dif = amount - coins[i];
            res = coinChange(coins, dif, v);
            if(res != -1){
                minI = min(res+1, minI);
            }
        }
        return v[amount] = (minI == INT_MAX? -1:minI); 
    }
    
    int coinChange(vector<int>& coins, int amount) {
        sort(coins.begin(), coins.end(), [](const int& a, const int& b){
            return a > b;
        });
        
        vector<int> v(amount+1, 0);
        
        return coinChange(coins, amount, v);
    }
};
```

Apparently it's slower than I thought. So I tried with BFS too.



### w/ BFS

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // in BFS, order of coins won't affect as much.
        // sort(coins.begin(), coins.end(), [](const int& a, const int& b){
        //     return a > b;
        // });
        
        // vector<int> v(amount+1, 0);
        int v[10001]; // this would be faster than vector
        
        int minI;
        for(int i = 1; i <= amount; i++){
            minI = INT_MAX;
            for(const auto& coin:coins){
                if(i - coin >= 0 && v[i-coin] != -1){
                    minI = min(v[i-coin] + 1, minI);
                }
            }
            v[i] = minI == INT_MAX? -1:minI;
        }
        
        return v[amount];
    }
};
```



So... BFS makes it around 2x faster.



|      | Time   | Space   |
| ---- | ------ | ------- |
| DFS  | 168 ms | 14.5 MB |
| BFS  | 88 ms  | 10.1 MB |


## 3. Epilogue 

What I've learned from this exercise:

- I should be able to do DP with top-down(DFS) and also bottom-up(BFS).
- It may seem difficult but essentially it's quite intuitive and easy.
