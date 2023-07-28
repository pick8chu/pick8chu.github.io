---
title: Predict the Winner
last_updated: July 28, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: predict-the-winner.html
---

## #. Problem & What to Remember

### <a href="https://leetcode.com/problems/predict-the-winner/" target="_blank">Go to the problem on leetcode</a>


## 1. My approach

Bruteforce for all the possible cases would take _O(2^n)_.
However, it says each player would choose **optimally.**
Assuming, each player would try all the possible answers and will choose it for the maximum score.

Each player has 2 different choices.
1. get it from the front,
2. get it from the back.

And each player has to see which one has a better possibility.
So this has to be done by recursive. Top-bottom fashion.

Check the code for more explanation.

```typescript
function PredictTheWinner(nums: number[]): boolean {
    const n = nums.length;

    function maxDiff(left: number, right: number): number {
        //At this point, just return the last possible number.
        if (left === right) return nums[left];

        /*
        ** scoreByLeft: the possible differences in scores when choosing it from the front.
        ** 'maxDiff(left+1, right)' will calculate the best possible score for the opponent.
        ** It will recursively come out with the possible maximum score differences.
        ** scoreByRight will function the same way but when choosing from behind.
        */
        const scoreByLeft = nums[left] - maxDiff(left + 1, right);
        const scoreByRight = nums[right] - maxDiff(left, right - 1);

        return Math.max(scoreByLeft, scoreByRight);
    }

    return maxDiff(0, n - 1) >= 0;
};
```

| Time (O(2^n))   | Space O(1) |
| ------- | ------- |
| 232 ms | 43 MB |


## 2. DP
Since the recursive would continuously have it do the same calculation, we can think about storing those in a nested array.

```typescript
function PredictTheWinner(nums: number[]): boolean {
    const n = nums.length;
    const dp:number[][] = Array.from({length: n}, () => []);

    function maxDiff(left: number, right: number): number {
        if (left === right) return nums[left];

        if(!dp[left+1][right]) dp[left+1][right] = maxDiff(left + 1, right);
        if(!dp[left][right-1]) dp[left][right-1] = maxDiff(left, right - 1);

        const scoreByLeft = nums[left] - dp[left+1][right];
        const scoreByRight = nums[right] - dp[left][right-1];
        
        return Math.max(scoreByLeft, scoreByRight);
    }

    return maxDiff(0, n - 1) >= 0;
};
```

OR

```typescript
function PredictTheWinner(nums: number[]): boolean {
    const n = nums.length;
    const dp:number[][] = Array.from({length: n}, () => []);

    function maxDiff(left: number, right: number): number {
        if (left === right) return nums[left];

        const scoreByLeft = nums[left] - (dp[left+1][right] || maxDiff(left + 1, right));
        const scoreByRight = nums[right] - (dp[left][right-1] || maxDiff(left, right - 1));
        dp[left][right] = Math.max(scoreByLeft, scoreByRight);

        return dp[left][right];
    }

    return maxDiff(0, n - 1) >= 0;
};
```

Time complexity would be _the time of filling n*n array_:

| Time (O(n^2))   | Space O(1) |
| ------- | ------- |
| 54 ms | 43 MB |


## 3. Epilogue 

What I've learned from this exercise:

- DP is such a headache...
- **Be used to do top-down approach**
