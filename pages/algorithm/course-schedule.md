---
title: leetcode- Course Schedule
last_updated: Aug 8nd, 2025
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: course-schedule.html
summary: topological sort

---

## #. Problem

[Problem link](https://leetcode.com/problems/course-schedule/)


## 1. My approach 

The main objective was to not make a cycle in a graph, which I went for Union-find.

```javascript
/**
 * @param {number} numCourses
 * @param {number[][]} prerequisites
 * @return {boolean}
 */
var canFinish = function(numCourses, prerequisites) {
    const arr = Array.from({length: numCourses}, () => -1);

    const findRoute = (arr, a) => {
        if (arr[a] == -1) return a;
        arr[a] = findRoute(arr, arr[a]);
        return arr[a];
    }

    const update = (arr, from, to) => {
        arr[to] = findRoute(arr, from)
        console.log(`from -> to : ${from} -> ${to}`);
        console.log(arr);
    }

    const isLoop = (arr, from, to) => {
        const routeFrom = findRoute(arr, from);
        return routeFrom == to;
    }

    const sorted = prerequisites.sort((a, b) => a[1] - b[1] ? a[1] - b[1] : a[0] - b[0]);

    for(const [a,b] of sorted){
        if(isLoop(arr, b, a)) return false;
        update(arr, b, a); 
    }

    return true;
};
```

And I realized that this has a direction, I found out that it won't work.
Also, with the prerequisites, if I go brute-force, it can be exponential, as 1 vertex can have N-1 roots, and these N-1 roots can also have N-2 roots.

## 2. Solution (Topological sort)

I found out this algorithm here and there, but this website was very [intuitive](https://www.interviewcake.com/concept/java/topological-sort). 
Basically, finding nodes with no edges directed to, and removing those. Continuing this process would give you a good view of the whole structure or prerequisites. 
If you cannot find any nodes that have 0 edges directed to, that means it will make a cycle, which means it cannot be done.

First approach was very straight forward with floyd-warshall 

```javascript
/**
 * @param {number} numCourses
 * @param {number[][]} prerequisites
 * @return {boolean}
 */
var canFinish = function(numCourses, prerequisites) {
    // const topological = [];
    let newPre = [...prerequisites];
    let isRemoved = true

    while(isRemoved && newPre.length){
        isRemoved = false;

        const followed = Array.from({length: numCourses}, () => 0);
        for(const [to, from] of newPre){
            followed[to] = 1;
        }

        for(const i in followed){
            if(!followed[i]) {
                newPre = newPre.filter(el => el[1] != i);
                isRemoved = true;
            }
        }
    }

    return newPre.length == 0;
};
```

And I got Time Limit Error, due to N^3. (2000^3)
So I changed the code more efficient.

```javascript
/**
 * @param {number} numCourses
 * @param {number[][]} prerequisites
 * @return {boolean}
 */
var canFinish = function(numCourses, prerequisites) {
    const q = [];
    const m = Array.from({length: numCourses}, () => Array.from({length: numCourses}, () => 0));
    const followedBy = Array.from({length: numCourses}, () => 0);

    // 5000
    for(const [to, from] of prerequisites){
        followedBy[to] += 1;
        m[from][to] = 1;
    }

    // 2000 
    for(const i in followedBy){
        if(!followedBy[i]) q.push(i);
    }

    // 2000 
    while(q.length){
        const v = q.shift();
        let hasDeleted = false;

        // deleting and adding to queue, 2000
        for(let i = 0; i < numCourses; i++){
            if(m[v][i] > 0){
                followedBy[i] -= 1;
                m[v][i] = 0;
                hasDeleted = true;
                if(followedBy[i] == 0) q.push(i);
            }
        }
    }

    // 2000
    return followedBy.reduce((acc, el) => acc += el, 0) == 0;
};
```

and as you can see it barely made it. Weird, it's only 2000^2, but right below 1000 ms.

| Time - O(n^2) | Space - O(n^2) |
| ----------- | ------------ |
| 993 ms       | 97.2 MB        |

So asked Chat GPT and got the better idea than using matrix.
Matix is useless since I don't really check regarding that, and once a node is removed, that's basically removing 1 row and 1 column from the matrix.
So traversing through the connected edges would be a better approach and faster.

Also, shift is adding another N complexity, which should not be much, but still.

So here is the final code:

```javascript
/**
 * @param {number} numCourses
 * @param {number[][]} prerequisites
 * @return {boolean}
 */
var canFinish = function(numCourses, prerequisites) {
    const q = [];
    const followedBy = Array.from({length: numCourses}, () => 0);
    const following = Array.from({length: numCourses}, () => []);

    // 5000
    for(const [to, from] of prerequisites){
        followedBy[to] += 1;
        following[from].push(to);
    }

    // 2000 
    for(const i in followedBy){
        if(!followedBy[i]) q.push(i);
    }

    let ptr = 0;

    // 2000 
    while(ptr < q.length){
        const v = q[ptr];
        ptr++;

        // it's +5000, not *5000
        // because it doesn't go over the edges that were already traveled.
        for(const to of following[v]){
            followedBy[to] -= 1;
            if(followedBy[to] == 0) q.push(to);
        }
    }

    // Therefore this is O(V+E)

    // 2000
    return followedBy.reduce((acc, el) => acc += el, 0) == 0;
};
```


| Time - O(V+E) | Space - O(V+E) |
| ----------- | ------------ |
| 19 ms       | 61.4 MB        |


## 3. Epilogue 

What I've learned from this exercise:
- Same logic can be code differently for the best optimal courses
