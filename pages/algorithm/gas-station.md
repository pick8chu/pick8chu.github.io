---
title: leetcode- Gas Station
last_updated: July 26th, 2024
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: gas-station.html
summary: Greedy algorithm

---

## #. Problem

[Problem link](https://leetcode.com/problems/gas-station)


## 1. My approach 

N is too big. It cannot be O(n^2).
Thus, I thought it'd be DP, so I made arrays - but didn't work out.

## 2. Solution

The important clue was that if it works, it'd have only one unique solution.
**Which means, if it is possible(total gas >= total cost), then in anytime if cost is bigger than gas, it cannot be possible. Therefore, you can start over from that point.
Thus it's greedy. It obtain the optimal solution in each steps, and contains the result (possible/not possible) from 0 to N steps.**

This is the key to the solution - without this, you would simply do N^2 loops.
Because you can assure that, if from 0 to N is failed, it'll be always failed even wherever you start between 0 to N. 

This solution is O(2n), which is O(n).
```javascript
/**
 * @param {number[]} gas
 * @param {number[]} cost
 * @return {number}
 */
var canCompleteCircuit = function(gas, cost) {
    const isPossible = gas.reduce((acc,el) => acc+=el,0) >= cost.reduce((acc,el) => acc+=el,0);
    if(!isPossible) return -1;

    let startIdx = 0;
    let res = 0;
    let tank = gas[startIdx];
    for(let count = 0; count < gas.length; count++){
        tank -= cost[startIdx];

        startIdx = startIdx < gas.length-1? startIdx+1:0;

        if(tank < 0){
            tank = gas[startIdx];
            res = startIdx;
            /**
             * count = 0 is to ensure that from the new startIdx, it actually achieves the goal.
             * However, since we know as the constraints that if it is possible, 
             * it'll have only one unique solution.
             * Therefore, we don't really need to check as doing N loops.
             */
            count = 0;
            continue;
        } else {
            tank += gas[startIdx];
        }
    }
    return res;
};
```



| Time - O(n) | Space - O(1) |
| ----------- | ------------ |
| 80 ms       | 61 MB        |



## 3. Epilogue 

What I've learned from this exercise:

It took me way too long.
The approach is very cleaver, and totally makes sense.
It required less of technical knowledge but more of critical thinking.