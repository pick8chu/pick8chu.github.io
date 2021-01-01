---
title: leetcode- buy and sell stock 2
last_updated: Nov 5, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: buy_and_sell_stock_2.html
summary: Greedy algorithm

---

## #. Problem

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii



## 1. My approach (O(n^n))

So, I had no idea how to solve this, and had wrong concept of its solution. Thereby, i just started coding of brute force algorithm not knowing how long it'll take.

```c++

#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int dfs(vector<int>& prices, int cur, int deep);

int dfsInit(vector<int>& prices, int end) {
    int ans = 0;
    for (int i = end; i > 0; i--) {
        ans = max(ans, dfs(prices, i, 0));
    }
    return ans;
}

int dfs(vector<int>& prices, int cur, int deep) {
    if (cur < 0) return 0;

    int sell = prices[cur];
    int tmp = 0;
    int ans = 0;
    for (int i = cur - 1; i >= 0; i--) {
        if (sell > prices[i]) {
            tmp = sell - prices[i];
            int tmp2 = dfsInit(prices, i - 1);
            ans = max(ans, tmp + tmp2);
        }
    }
    return ans;
}

int maxProfit(vector<int>& prices) {
    int ans = 0;
    ans = dfsInit(prices, prices.size() - 1);
    return ans;
}
```

It exceeded the time limit.

| Time - O(n^n) | Space - O(1) |
| ------------- | ------------ |
| ? ms          | ? MB         |

## 2. How you supposed to approach (O(n))

Peak Valley Approach was how you supposed to go with. I didn't understand the problem itself. As you might already know, the total profit is:
$$
totalProfit= ∑_i(height(peak_i ))-(height(valley_i ))
$$
explanation and pictures are in [here(the solution page)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/solution/).

```java
class Solution {
    public int maxProfit(int[] prices) {
        int i = 0;
        int valley = prices[0];
        int peak = prices[0];
        int maxprofit = 0;
        while (i < prices.length - 1) {
            while (i < prices.length - 1 && prices[i] >= prices[i + 1])
                i++;
            valley = prices[i];
            while (i < prices.length - 1 && prices[i] <= prices[i + 1])
                i++;
            peak = prices[i];
            maxprofit += peak - valley;
        }
        return maxprofit;
    }
}
```



I actually haven't coded this one since I didn't know if valleys and peaks are so important. I only came up with the next solution, which i think I was very ... lucky?



| Time - O(n) | Space - O(1) |
| ----------- | ------------ |
| ? ms        | ? MB         |



## 3. Simple One Pass

So if you see from the graph of [the solution page](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/solution/), you may notice that the total profit is total sum of every inclines except the first one. Here is the solution of it. 

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        vector<int> ans(prices.size(), 0);
        
        for(int i = 1; i < prices.size(); i++){
            ans[i] = (prices[i] - prices[i-1] < 0? 0:prices[i] - prices[i-1]) + ans[i-1];
        }
        
        return ans.back();
    }
};
```



I honestly don't think I explained it well, so I recommend you to just go to leetcode and see the solution on their website. I only wanted to post it here just to not forget about the concept of greedy algorithm.

| Time - O(n) | Memory - O(1) |
| ----------- | ------------- |
| 12 ms       | 13.7 MB       |



## 4. Epilogue 

What I've learned from this exercise:

- You have to find out the fundamental logic behind the problem (which can be quite difficult in many times).
