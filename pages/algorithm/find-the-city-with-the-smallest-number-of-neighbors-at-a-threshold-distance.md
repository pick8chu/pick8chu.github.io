---
title: leetcode- Find the City With the Smallest Number of Neighbors at a Threshold Distance
last_updated: July 26th, 2024
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance.html
summary: All pair shortest path

---

## #. Problem

[Problem link](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance)


## 1. My approach 

All pair shortest path - O(n^3)

Not very optimal on readability, but here it is:
```javascript
/**
 * @param {number} n
 * @param {number[][]} edges
 * @param {number} distanceThreshold
 * @return {number}
 */
var findTheCity = function(n, edges, distanceThreshold) {
    const dp = Array.from({length: n}, el => Array.from({length:n}, el => 0));

    for(const [u, v, weight] of edges){
        dp[u][v] = weight;
        dp[v][u] = weight;
    }

      for(let u = 0; u < n; u++){
        for(let v = 0; v < n; v++){
            if(u === v) continue;
            for(let w = 0; w < n; w++){
                if(u === w || v === w) continue;
                const uToW = dp[u][w];
                const wToV = dp[w][v];
                const uToV = dp[u][v];
                if(!uToW || !wToV) continue;
                
                const UtoWtoV = uToW + wToV;
                if(!uToV || UtoWtoV < uToV){
                    dp[u][v] = uToW + wToV;
                    dp[v][u] = uToW + wToV;
                }
            }
        }
    }

    const possibleCities = [];
    let minNum = 999;
    for(let u = 0; u < n; u++){
        let count = 0;
        for(let v = 0; v < n; v++){
            if(!dp[u][v]) continue;
            if(dp[u][v] <= distanceThreshold) count++; 
        }
        possibleCities.push(count);
        minNum = Math.min(minNum, count);
    }

    return possibleCities.lastIndexOf(minNum);
};
```

This encountered a problem on specific conditions.
`w` should be the most outer loop, because by putting `w` inside the inner loop, it make array `dp` not optimal. 
On the time when `dp[u][w] + d[w][v] < dp[u][v]`, it has to be ensured that `dp[u][w]` and `dp[w][v]` is containing the shortest path already.

So by putting `w` outside the inner loop, it's solved.

## 2. Solution(refactoring)

```javascript
/**
 * @param {number} n
 * @param {number[][]} edges
 * @param {number} distanceThreshold
 * @return {number}
 */
var findTheCity = function(n, edges, distanceThreshold) {
    // Initialize all distances to Infinity - not reachable from any city
    const dp = Array.from({length: n}, () => Array.from({length: n}, () => Infinity));
    
    // Set the direct distances from the edges and 0 for the diagonal
    for (let i = 0; i < n; i++) {
        dp[i][i] = 0;
    }
    
    for (const [u, v, weight] of edges) {
        dp[u][v] = weight;
        dp[v][u] = weight;
    }

    // Floyd-Warshall algorithm to find all-pairs shortest paths
    for (let k = 0; k < n; k++) {
        for (let i = 0; i < n; i++) {
            for (let j = 0; j < n; j++) {
                if (dp[i][k] + dp[k][j] < dp[i][j]) {
                    dp[i][j] = dp[i][k] + dp[k][j];
                }
            }
        }
    }

    // Find the city with the smallest number of cities within the distance threshold
    let minNum = Infinity;
    let resultCity = -1;
    
    for (let i = 0; i < n; i++) {
        let count = 0;
        for (let j = 0; j < n; j++) {
            if (i !== j && dp[i][j] <= distanceThreshold) {
                count++;
            }
        }
        if (count <= minNum) {
            minNum = count;
            resultCity = i;
        }
    }
    
    return resultCity;
};

```


| Time - O(n^3) | Space - O(n^2) |
| ----------- | ------------ |
| 84 ms       | 55 MB        |



## 3. Epilogue 

What I've learned from this exercise:

small details ... focus.