---
title: leetcode- Best Time to Buy and Sell Stock with Transaction Fee
last_updated: March 17th, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: best-time-to-buy-and-sell-stock-with-transaction-fee.html
summary: Greedy algorithm/DP

---

## #. Problem

[Problem link](https://leetcode.com/problems/https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)



## 1. My approach 

This is actually a problem that I was asked on an interview, and I honestly had no idea how to solve it. And Thankfully I had a chance to look at it once more on Leetcode, but I still couldn't even guess how to.



## 2. Solution

1. [Solution page](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/solution/)
2. [Discussion page](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/discuss/1112543/C%2B%2B-36ms-Greedy-DP-Solution-Explained-100-Time-100-Space)



It took me for a while to actually understand the meanings of variables. I always thought DP is difficult to understand with codes.



```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int potential_balance = -prices[0];
        int balance = 0;
        for(const auto& p : prices){
            // whether you are not selling it(balance),
            // or sell it which you bought it at 'potential_balance'
            balance = max(balance, p - (-potential_balance) - fee);
            
            potential_balance = max(potential_balance, balance - p);
        }
        return balance;   
    }
};
```



### *potential_balance*

'*potential_balance*' in '*i*' index is a potential balance which, considering you bought it on '*prices[i]*', how much you'll have. For example, it starts with '*potential_balance = -prices[0]*', which means, since you have 0 '*balance*', if you were to buy a stock at the moment, that will be '*-prices[0]*'.



### *balance*

'*balance*' is how much you have at the moment.



Try to do it with your hands, step by step, it'll help you to understand.

| Time - O(n) | Space - O(1) |
| ----------- | ------------ |
| 92 ms       | 55 MB        |



## 3. Epilogue 

What I've learned from this exercise:

- To understand DP, it's better to follow it step by step with your own hands.
