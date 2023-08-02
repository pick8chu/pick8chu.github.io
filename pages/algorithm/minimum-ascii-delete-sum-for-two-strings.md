---
title: Minimum ASCII Delete Sum for Two Strings
last_updated: Aug 2, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: minimum-ascii-delete-sum-for-two-strings.html
---

## #. Problem & What to Remember

### <a href="https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/" target="_blank">Go to the problem on leetcode</a>


## 1. My approach (DP sucks)

Bruteforce for all the possible cases, but it doesn't seem that straightforward for this one.
Normally, I would try to remove one character each and see if that would be the same as the other string.
Obviously, it would exceed the time limit.


## 2. DP

The idea is, to store the optimal score at the moment. 
This attempt also would calculate all the possible scenarios.

This code below is basically the most important part. 
As recursively calling `cal`, it would let the DP work.

Quite brilliant.

```typescript
res = Math.min(
    s1[i].charCodeAt(0) + cal(i-1, j),
    s2[j].charCodeAt(0) + cal(i, j-1),
    s1[i].charCodeAt(0) + s2[j].charCodeAt(0) + cal(i-1, j-1)
)
```

```typescriptfunction minimumDeleteSum(s1: string, s2: string): number {
    const [n1, n2] = [s1.]
    const dp: number[][] =  [];

    const cal = (i:number, j:number) : number => {
        let delCost;
        if(i < 0){
            delCost = 0;
            for(let idx = 0; idx <= j; idx++){
                delCost += s2[idx].charCodeAt(0);
            }
            return delCost;
        };

        if(j < 0){
            delCost = 0;
            for(let idx = 0; idx <= i; idx++){
                delCost += s1[idx].charCodeAt(0);
            }
            return delCost;
        }
        
        if(!dp[i-1]) dp[i-1] = []
        if(!dp[i]) dp[i] = []
        
        let res;

        if(s1[i] === s2[j]){
            res = dp[i-1][j-1] || cal(i-1, j-1);
        } else {
            res = Math.min(
                s1[i].charCodeAt(0) + (dp[i-1][j] || cal(i-1, j)),
                s2[j].charCodeAt(0) + (dp[i][j-1] || cal(i, j-1)),
                s1[i].charCodeAt(0) + s2[j].charCodeAt(0) + (dp[i-1][j-1] || cal(i-1, j-1))
            );
        }
        dp[i][j] = res;
        return dp[i][j];
    }

    return cal(s1.length-1, s2.length-1);
};
```

Time complexity would be _the time of filling n*n array_:

| Time (O(n^2))   | Space O(1) |
| ------- | ------- |
| 54 ms | 43 MB |


## 3. Epilogue 

What I've learned from this exercise:

So once you come up with the bruteforce,
if you find a way** make every steps optimal** and **can store the data at each step**,
that would make an optimal DP solution for the whole problem.

- DP is such a headache...
- **Be used to do top-down approach**
