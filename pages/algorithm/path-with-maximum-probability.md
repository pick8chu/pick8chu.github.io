---
title: Path with Maximum Probability
last_updated: July 25, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: path-with-maximum-probability.html
---

## #. Problem & What to Remember

### [Go to the problem on leetcode](https://leetcode.com/problems/path-with-maximum-probability/)
### Dijkstra is only possible with Priority Queue!

## 1. My approach

It's a single source shortest path problem.
Due to it's *0.#* number, multiplying would make the value smaller. Therefore, it will make no difference from the traditional graph.

My mistake was, not using the Priority queue. I was basically doing BFS - but as doing so, I found a case that cannot be succeeded by my approach.

Here is the possible case:
{% include image.html file="graph-pq-needed.png"  %}

In this example, if we start from 0, according to my approach(BFS), vertex 1 would be processed before vertex 5.
But as you can see, processing vertex 1 before processing vertex 5 won't be the optimal solution.

Dijkstra, using dynamic programming, won't work if the problem is not optimal. Meaning, in every stage, the solution has to be the best optimal solution.
To rectify this, Priority Queue is introduced.
(JS doesn't have the standard library for PQ so I just sorted a list in every step)

```typescript
function maxProbability(n: number, edges: number[][], succProb: number[], start: number, end: number): number {

    type Edge = {
        to: number;
        val: number;
    };

    type Ver = {
        v: number;
        curVal: number;
    }

    const pq: Ver[] = [];
    const dp: number[] = [];
    const visited: boolean[] = [];
    const edgeList: Edge[][] = Array.from({length: n+1}, () => []);

    for(let i = 0; i < edges.length; i++){
        edgeList[edges[i][0]].push({
            to: edges[i][1],
            val: succProb[i],
        });

        edgeList[edges[i][1]].push({
            to: edges[i][0],
            val: succProb[i],
        });
    }

    pq.push({v:start, curVal:1});
    dp[start] = 1;

    while(pq.length > 0){
        const {v} = pq.shift();
        if(visited[v]) continue;

        for(const edge of edgeList[v]){
            const {to, val} = edge;

            dp[to] = Math.max(dp[to]? dp[to]:-1, dp[v] * val);
            if(!visited[to]) pq.push({v:to, curVal: dp[to]}); 
        }

        visited[v] = true;
        pq.filter(a => visited)
        pq.sort((a, b) => b.curVal - a.curVal);

        // console.log(`${v} ::::`)
        // console.log(dp);
    }

    return dp[end]? dp[end]:0;
};
```

| Time (O(V*E))   | Space O(V+E) |
| ------- | ------- |
| 7525 ms | 108.7 MB |


## 2. Epilogue 

Dijkstra has PQ inside!

What I've learned from this exercise:

- Be more flexible...
