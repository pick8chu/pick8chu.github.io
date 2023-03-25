---
title: Count Unreachable Pairs of Nodes in an Undirected Graph
last_updated: Mar 25, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: count-unreachable-pairs-of-nodes-in-an-undirected-graph.html
---

## #. Problem

[Go to the problem on leetcode](https://leetcode.com/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/)


## 1. My approach

It was pretty obvious to use union-find to solve this problem. The problem was, that *n* was too big. *O(N^2)* was not going to work. But there seems to be no other ways. <br/> 

```typescript
function countPairs(n: number, edges: number[][]): number {
    const set = Array.from({length: n}, (_, idx) => idx);
    const sizeArr = Array.from({length: n}, () => 0);
    const queue = [];

    const find = (x: number):number => {
        if(set[x] === x) return x;
        else return set[x] = find(set[x]);
    }

    const union = (x: number, y: number):void => {
        const setX = find(x); 
        const setY = find(y); 

        if(setX !== setY) set[setX] = setY;
    }

    edges.forEach(([x, y]) => union(x, y));

    for(let i = 0; i < n; i++) sizeArr[find(i)]++;

    for(let i = 0; i < n; i++) {
        if(sizeArr[i]) queue.push(sizeArr[i]);
    }

    let notConnected = 0;

    // *********************************************
    // this is possibly O(N^2). 10^5 * 10^5. No chance.
    // *********************************************
    for(let i = 0; i < queue.length; i++ ){
        for(let j = i+1; j < queue.length; j++){
            const temp = queue[i] * queue[j];
            if(temp) notConnected += temp;
        }
    }
    // *********************************************

    return notConnected;

};

```

Thanksfully, I could use a bit of segway to solve this problem. However, it still have *O(N^2)* on worst case.<br/>


| Time (O(N^2))   | Space O(N) |
| ------- | ------- |
| 6739 ms | 88.7 MB |


## 2. Better approach with O(N)

So this one, I was not able to see it, but also very straight forward. <br/>
Basically, I just needed to know how many vertices are left at the moment when I calculate the number of not connected pairs. <br/>

```typescript
function countPairs(n: number, edges: number[][]): number {
    const set = Array.from({length: n}, (_, idx) => idx);
    const map = Array.from({length: n}, () => 0);

    const find = (x: number):number => {
        if(set[x] === x) return x;
        else return set[x] = find(set[x]);
    }

    const union = (x: number, y: number):void => {
        const setX = find(x); 
        const setY = find(y); 

        if(setX !== setY) set[setX] = setY;
    }

    edges.forEach(([x, y]) => union(x, y));

    for(let i = 0; i < n; i++) {
        const idx = find(i);
        map[idx] = map[idx]? map[idx]+1:1;
    }

    let notConnected = 0;
    let remainVertices = n;

    // *********************************************
    // this is O(N). Very simple, clever.
    // *********************************************
    for(const key in map){
        console.log(map[key]);
        remainVertices -= map[key];
        notConnected += map[key] * remainVertices;
    }
    // *********************************************

    return notConnected;

};
``` 

| Time (O(N^2))   | Space O(N) |
| ------- | ------- |
| 1286 ms | 91.8 MB |

## 3. Epilogue 

It only takes tiny bit of wisdom to make a huge differeces. <br/>

What I've learned from this exercise:

- Be more flexible...