---
title: leetcode- Create Sorted Array through Instructions
last_updated: Jan 01, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: create-sorted-array-through-instructions.html

---

## #. Problem

[Create Sorted Array through Instructions](https://leetcode.com/problems/create-sorted-array-through-instructions/)



## 1. My approach

### Using Map and array, with binary insert

This one was quite tricky, but worked(very slowly). The idea was, using a map to save how many redundant numbers they have and subtract them when you needed. and make an array and whenever you need to insert, use binary search and insert in the right place.

It sounds like it's gonna work but the time complexity would be not ideal. O(N * log N * N) would be the time complexity. 1~N, binary search and splice.

I'm quite surprised it passed anyways.

```javascript
/**
 * @param {number[]} instructions
 * @return {number}
 */
var createSortedArray = function(instructions) {
    
    let map = new Map();
    let arr = [Math.min(instructions[0], instructions[1]), Math.max(instructions[0], instructions[1])];
    map.set(arr[0], 1);
    map.set(arr[1], 1);
    
    let binary_insert = (arr, target) => {
        let left = 0;
        let right = arr.length-1;
        while(left <= right){
            let idx = Math.floor((left+right)/2);
            if(arr[idx] <= target && target < arr[idx+1]){
                arr.splice(idx+1, 0, target);
                return Math.min(idx+1-(map.get(target)-1), arr.length-1-(idx+1));
            }
            
            if(arr[idx] > target) right = idx-1;
            else left = idx+1;
        }
        return NaN;
    }
    
    let add = (arr, target) => {
        if(target <= arr[0]) {
            arr.unshift(target);
            return 0;
        }
        else if(arr[arr.length-1] <= target){
            arr.push(target);
            return 0;
        }
        else {
            return binary_insert(arr, target);
        }
    }
    
    let ans = 0;
    
    for(let i = 2; i < instructions.length; i++){
        map.set(instructions[i], map.get(instructions[i])? map.get(instructions[i])+1: 1);
        ans += add(arr, instructions[i]);
        ans %= 1000000007;
    }
    
    return ans;
};
```

| Time    | Space   |
| ------- | ------- |
| 5844 ms | 84.3 MB |

## 2. Solution(Fenwick Tree/Segment Tree)

> Both would work for this, but it would have dramatic differences in time complexity, Fenwick only needs (max Element)+1 length, but Segment Tree needs (max Element)*4 length, and of course it would take more time to update or get sum of certain range.



### Implement

#### Fenwick Tree

```javascript
/**
 * @param {number[]} instructions
 * @return {number}
 */
var createSortedArray = function(instructions) {
    
    const maxEl = instructions.reduce((r, el) => Math.max(r, el)) + 1;
    const arr = Array.from({length:maxEl}, ()=>0);
    let ans = 0;
    
    let update_el = (idx, diff) => {
        while(idx < maxEl){
            arr[idx] += diff;
            idx += idx & (-idx);
        }
    }
    
    let sum_to = (to) => {
        let res = 0;
        while(to > 0){
            res += arr[to];
            to -= to & (-to);
        }
        return res;
    }
    
    let sum_from_to = (from, to) => {
        if(from > to) return 0;
        else return sum_to(to) - sum_to(from-1);
    }
    
    for(let i of instructions){
        update_el(i, 1);
        ans += Math.min(sum_from_to(1, i-1), sum_from_to(i+1, maxEl-1));
        ans %= 1000000007;
    }
    
    return ans;
    
};
```


#### Segment Tree

```javascript
/**
 * @param {number[]} instructions
 * @return {number}
 */
var createSortedArray = function(instructions) {
    
    const maxEl = instructions.reduce((r, el) => Math.max(r, el));
    const arr = Array.from({length:(100000+1)*4}, ()=>0);
    let ans = 0;
    
    let update_el = (root, left, right, idx, diff) => {
        if(idx < left || right < idx) return arr[root];
        
        if(left === right) {
            return arr[root] += diff;
        }
        
        let mid = Math.floor((left+right)/2);
        return arr[root] = update_el(root*2, left, mid, idx, diff) + update_el((root*2)+1, mid+1, right, idx, diff);
    }
    
    let query_ans = (root, left, right, q_left, q_right) => {
        if(q_left > q_right) return 0;
        if(q_right < left || right < q_left) return 0;
        
        if(q_left <= left && right <= q_right) {
            return arr[root];
        }
        
        let mid = Math.floor((left+right)/2);
        return query_ans(root*2, left, mid, q_left, q_right) + query_ans((root*2)+1, mid+1, right, q_left, q_right);
    }
    
    for(let i of instructions){
        update_el(1, 1, maxEl, i, 1);
        ans += Math.min(query_ans(1, 1, maxEl, 1, i-1), query_ans(1, 1, maxEl, i+1, maxEl));
        ans %= 1000000007;
    }
    
    return ans;
    
};
```


|                        | Time    | Space |
| ---------------------- | ------- | ----- |
| Fenwick Tree (N log N) | 240 ms  | 53 MB |
| Segment Tree (N log N) | 2232 ms | 60 MB |



## 3. Epilogue 

What I've learned from this exercise:

- Fenwick Tree and Segment Tree are both very useful, but knowing when to use what is very essential to time efficiency.
