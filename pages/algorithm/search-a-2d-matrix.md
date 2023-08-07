---
title: Search a 2D Matrix
last_updated: Aug 7, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: search-a-2d-matrix.html
---

## #. Problem

### <a href="https://leetcode.com/problems/search-a-2d-matrix/" target="_blank">Go to the problem on leetcode</a>


## 1. My approach (binary search x2)

Easy solution.

```typescript
function searchMatrix(arr: number[][], target: number): boolean {
    
    let [l, r]:[number, number] = [0, arr.length-1];
    let mid = Math.floor((l+r)/2);

    while(l < r){
        if(arr[mid][0] === target){
            break;
        } else if(arr[mid][0] < target){
            l = mid+1;
        } else {
            r = mid-1;
        }

        mid = Math.floor((l+r)/2);
    }

    if(!arr[mid]) return false;
    if(mid > 0 && arr[mid][0] > target) mid--;

    l = 0;
    r = arr[mid].length;
    let colMid = Math.floor((l+r)/2);
    while(l < r){
        if(arr[mid][colMid] === target){
            break;
        } else if(arr[mid][colMid] < target){
            l = colMid+1;
        } else {
            r = colMid-1;
        }

        colMid = Math.floor((l+r)/2);
    }

    console.log(mid, colMid)
    return (arr[mid][colMid] === target);
};
```

_O(log(N+M))_

## 2. Easier Solution

Flatten the 2d array to 1d array and do the search.
So straight forward, can't believe I couldn't think about this.

```typescript
function searchMatrix(matrix: number[][], target: number): boolean {
    return (matrix.flatMap(array => array)).find(num => num === target) !== undefined ? true : false
};
```

Time complexity would be a bit tricky. *flatMap(?) * find(logN)*
Don't know what _flatMap_ would cost. 

## 3. Epilogue 

What I've learned from this exercise:

- Think about simple solution, also using existing libraries.
