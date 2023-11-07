---
title: Eliminate Maximum Number of Monsters
last_updated: Nov 7, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: eliminate-maximum-number-of-monsters.html
---

## #. Problem

### <a href="https://leetcode.com/problems/eliminate-maximum-number-of-monsters/" target="_blank">Go to the problem on leetcode</a>

## 1. My approach

Straightforward:

```typescript
function eliminateMaximum(dist: number[], speed: number[]): number {
    let monster = 0;
    
    let left = dist.map((el, idx) => Math.ceil(el/speed[idx]));
    left.sort((a,b) => a-b);
    
    while(left.length){
        const nextTurn = left[0];
        monster += Math.min(nextTurn, left.length);
        left.splice(0, nextTurn);
        left = left.map(el => el-nextTurn);

        if(left.length && left[0] <= 0) return monster;
    }

    return monster;
};
```

## 2. Better/easier solution

This approach is to remove the human way of thinking.
It's very intuitive and very easy to understand.
While the previous solution describes the human problem in code,
this solution digitizes the human problem into code adding only the necessary info.
Very impressive and easy.

```typescript
function eliminateMaximum(dist: number[], speed: number[]): number {
    const arrivalAt = dist.map((el, idx) => el / speed[idx]);
    arrivalAt.sort((a, b) => a - b);

    let idx = 1;
    while(idx < dist.length && arrivalAt[idx] > idx) idx++;

    return idx;
};
```

## 3. Epilogue 

What I've learned from this exercise:

- Digitizing the problem would make things way easier
