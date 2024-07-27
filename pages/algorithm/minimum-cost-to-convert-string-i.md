---
title: leetcode- Minimum Cost to Convert String I
last_updated: July 27th, 2024
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: minimum-cost-to-convert-string-i.html
summary: All pair shortest path

---

## #. Problem

[Problem link](https://leetcode.com/problems/minimum-cost-to-convert-string-i)


## 1. My approach 

All pair shortest path - O(n^3) - `n` here is the number of alphabets(26).
Therefore finding the shortest path between alphabets is O(26^3) = O(1).
Time complexity: O(m + n) where `m` is the length of `original` and `n` is the length of `source`.

```javascript
/**
 * @param {string} source
 * @param {string} target
 * @param {character[]} original
 * @param {character[]} changed
 * @param {number[]} cost
 * @return {number}
 */
var minimumCost = function(source, target, original, changed, cost) {
    const getNumber = (char) => char.charCodeAt() - 97;

    const graph = Array.from({length:26}, (el,idx1) => Array.from({length:26}, (el,idx2) => idx1===idx2? 0:Infinity));

    for(let idx = 0; idx < original.length; idx++){
        const u = getNumber(original[idx]);
        const v = getNumber(changed[idx]);
        const uvCost = cost[idx];

        graph[u][v] = Math.min(graph[u][v], cost[idx]);
    }

    for(let w = 0; w < 26; w++){
        for(let u = 0; u < 26; u++){
            for(let v = 0; v < 26; v++){
                if(graph[u][w] + graph[w][v] < graph[u][v]){
                    graph[u][v] = graph[u][w] + graph[w][v];
                }
            }
        }
    }
    
    let totalCost = 0;
    for(let idx = 0; idx < source.length; idx++){
        const u = getNumber(source[idx]);
        const v = getNumber(target[idx]);
        totalCost += graph[u][v];

        if(totalCost === Infinity) return -1;
    }

    return totalCost;
};
```

## 2. Solution

My apporach was the solution - or alternatively, dikstra's algorithm.

| Time - O(m + n) | Space - O(1) |
| ----------- | ------------ |
| 150 ms       | 62 MB        |



## 3. Epilogue 

What I've learned from this exercise:

- critical thinking - simplifying the problem.