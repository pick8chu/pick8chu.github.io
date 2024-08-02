---
title: leetcode- Minimum Swaps to Group All 1's Together II
last_updated: Aug 2nd, 2024
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: minimum-swaps-to-group-all-1s-together-ii.html
summary: sliding window

---

## #. Problem

[Problem link](https://leetcode.com/problems/minimum-swaps-to-group-all-1s-together-ii)


## 1. My approach 

At the end of the process, all the 1's will be grouped together, and the rest are 0's
Therefore, eventually, this problem is to find a subset that is size of `x`, and has the smallest numbers of 0's.
`x` is the total number of 1's in the array.

Not very optimal on readability, but here it is:

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var minSwaps = function(nums) {
    const zeros = nums.filter(el => el === 0).length;
    const ones = nums.length-zeros;

    let minGap = nums.length;
    for(let i = 0; i < nums.length; i++){
        let tempGap = 0;
        for(let j = i, count = 0; count < ones; count++, j = j < nums.length-1? j+1:0){
            if(nums[j] === 0) tempGap++;
        }
        minGap = Math.min(tempGap, minGap);
    }
    return minGap;
};
```

Time limit error for the possible O(n^2) solution.

## 2. Solution (Sliding window)

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var minSwaps = function(nums) {
    const zeros = nums.filter(el => el === 0).length;
    const ones = nums.length-zeros;

    // Check how many 0's in the first subset
    let zeroCount = 0;
    for(let i = 0; i < ones; i++){
        if(nums[i] === 0) zeroCount++;
    }

    let currentZeroCnt = zeroCount;
    for(let i = 1; i < nums.length; i++){
        const j = ((i + ones - 1) % nums.length);
        
        /**
         * Sliding window:
         * Moving the window(subset) to the right, 
         * checking how many 0's are in the window.
         */
        if(nums[j] === 0) currentZeroCnt++;
        if(nums[i-1] === 0) currentZeroCnt--;

        zeroCount = Math.min(currentZeroCnt, zeroCount);
    }
    return zeroCount;
};
```


| Time - O(n) | Space - O(1) |
| ----------- | ------------ |
| 83 ms       | 60 MB        |



## 3. Epilogue 

What I've learned from this exercise:

- Fresh mind, focus!