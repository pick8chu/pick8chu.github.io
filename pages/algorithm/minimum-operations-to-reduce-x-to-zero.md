---
title: leetcode- Minimum Operations to Reduce X to Zero
last_updated: Jan 15, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: minimum-operations-to-reduce-x-to-zero.html

---

## #. Problem

[Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/)



## 1. My approach

### BFS

I was quite sure that it would be DP, but I couldn't think of any ways so I just used BFS just to see if it works. And obviously it didn't work. Time complexity would be O(2^N).


```javascript
/**
 * @param {number[]} nums
 * @param {number} x
 * @return {number}
 */
var minOperations = function(nums, x) {
    const q = [{
        curX: x,
        curArr: nums,
        curCnt: 0
    }];
    let ans = 0;
    
    while(q.length !== 0){
        let temp = q.shift();
        let curX = temp.curX;
        let curCnt = temp.curCnt;
        let curArr = temp.curArr;
        if(curX === 0){
            return temp.curCnt;
        }
        
        if(curX - curArr[0] >= 0){
            q.push({
                curX: curX - curArr[0],
                curArr: curArr.slice(1),
                curCnt: curCnt+1
            })
        }
        
        if(curArr.length > 1 && curX - curArr[curArr.length-1] >= 0){
            q.push({
                curX: curX - curArr[curArr.length-1],
                curArr: curArr.slice(0, curArr.length-1),
                curCnt: curCnt+1
            })
        }
        // console.log(q);
    }
    
    return -1;
};
```


| Time(O(2^N)) | Space(O(?)) |
| ---------------- | --------------- |
| ? ms           | ? MB         |

### super long DP

and I came up with this DP which was quite cleaver at the time but it was too big, making 'heap out of memory' error. 

- TC - O(N)
- SC - O(N)
- Where N = 1e9 :P

```javascript
/**
 * @param {number[]} nums
 * @param {number} x
 * @return {number}
 */
var minOperations = function(nums, x) {
    const arr = Array.from({length:x+1}, ()=>{
        return {
            curCnt: 100000,
            curLeft: 0,
            curRight: 0
        }
    });
    let i = 0, j = nums.length-1;
    arr[0].curCnt = 0;
    arr[0].curLeft = 0;
    arr[0].curRight = nums.length-1;
    
    for(let i = 0; i <= x; i++){
        let curCnt = arr[i].curCnt;
        let curLeft = arr[i].curLeft;
        let curRight = arr[i].curRight;
        
        if(i + nums[curLeft] <= x && curCnt + 1 < arr[i + nums[curLeft]].curCnt){
            arr[i + nums[curLeft]].curCnt = curCnt + 1;
            arr[i + nums[curLeft]].curLeft = curLeft + 1;
            arr[i + nums[curLeft]].curRight = curRight;
        }
        
        if(curLeft < curRight && i + nums[curRight] <= x && curCnt + 1 < arr[i + nums[curRight]].curCnt){
            arr[i + nums[curRight]].curCnt = curCnt + 1;
            arr[i + nums[curRight]].curLeft = curLeft;
            arr[i + nums[curRight]].curRight = curRight - 1;
        }
    }
    
    // console.log(arr);
    return arr[x].curCnt === 100000? -1:arr[x].curCnt;
    
};
```

## 2. Solution

It's called 'Sliding window', using two pointers, left and right. Starting from 0 and moving towards the end. Very cleaver, and efficient.

The time complexity here would be O(N) where N = 1e5, and space complexity would be, O(1). 
This time complexity might be confusing, but here's why it's O(N):
- O((right in 0 to N) + (left in 0 to k)) = O(N + k) = O(N) 



### Sliding window

```javascript
/**
 * @param {number[]} nums
 * @param {number} x
 * @return {number}
 */
var minOperations = function(nums, x) {
    
    const sum = nums.reduce((acc, cur) => acc + cur, 0);
    const target = sum - x;
    let curSum = 0;
    
    if(target < 0) return -1;
    if(!target) return nums.length;
    
    let l = 0, r = 0;
    let maxLen = -Infinity;
    
    while(r <= nums.length){
        curSum += nums[r];
        while(curSum > target) curSum -= nums[l++];
        if(curSum === target) maxLen = Math.max(r-l+1, maxLen);
        r++;
    }
    
    return maxLen === -Infinity? -1:nums.length-maxLen;
};
```


| Time(O(N)) | Space(O(1)) |
| ---------- | ----------- |
| 108 ms     | 50.6 MB     |



## 3. Epilogue 

What I've learned from this exercise:

- DP is difficult.
- JavaScript has 'Infinity' as a value of number type.
- Mostly, we approach from how we solve the problem in our head, but sometimes it's important to approach it differently since it might be better solution.
