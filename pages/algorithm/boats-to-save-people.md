---
title: leetcode- Boats to Save People
last_updated: Jan 14, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: boats-to-save-people.html

---

## #. Problem

[Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)



## 1. My approach

### Map with for loop

By putting numbers together, I thought it may be able to be more efficient. It wasn't too bad except I had to debugge for 30 mins, the logic itself wasn't so complicated, but had some edge cases. The time complexity would be O(30000^2) (length of Map^2).



```javascript
/**
 * @param {number[]} people
 * @param {number} limit
 * @return {number}
 */
var numRescueBoats = function(people, limit) {
    let m = new Map();
    let ans = 0;
    
    for(let el of people){
        m.set(el, m.get(el)? m.get(el)+1:1);
    }
    
    for(let el of m){
        if(el[1] === 0) continue;
        
        if(el[0] >= limit){
            ans += el[1];
            m.set(el[0], 0);
            continue;
        }
        else {
            let elCnt = el[1];
            for(let dif = limit - el[0];dif !== 0 && elCnt !== 0;dif--){
                let difCnt = m.get(dif);
                if(!!difCnt){
                    // console.log(el[0] + ',' + dif);
                    if(dif === el[0]) {
                        if(el[1] >= 2){
                            ans += Math.floor(elCnt/2);
                            elCnt %= 2;
                            m.set(el[0], elCnt);
                            continue;
                        }
                        else continue;
                    }
                    
                    if(elCnt > difCnt){
                        ans += difCnt;
                        m.set(dif, 0);
                        elCnt = elCnt - difCnt;
                        m.set(el[0], elCnt);
                    }
                    else {
                        ans += elCnt;
                        m.set(el[0], 0);
                        difCnt = difCnt - elCnt;
                        m.set(dif, difCnt);
                        elCnt = 0;
                    }
                }
            }
            if(elCnt){
                ans += elCnt;
                m.set(el[0], 0);
            }
        }
    }
    
    return ans;
    
};
```


| Time(O(30000^2)) | Space(O(30000)) |
| ---------------- | --------------- |
| 284 ms           | 50.1 MB         |



## 2. Solution

Detail Explanations are belows:

1. [Solution page](https://leetcode.com/problems/boats-to-save-people/solution/)

It uses two pointer, way simpler and more efficient. Why couldn't I see it... 

The time complexity here would be O(N log N) where N = 50000, and space complexity would be, O(N) (<- due to sorting algorithm.)

> Time complexity and space complexity depend on the implementation of each browsers.
>
> But the most common JavaScript engine (V8), which is used in Chromium browsers as well as Node.js, uses an algorithm known as [Timsort](https://en.m.wikipedia.org/wiki/Timsort), which has a worst-case time performance of O(n log n) and worst-case space complexity of O(n)
>
> by [James Lave](https://dev.to/workpebojot/what-time-and-space-complexity-of-javascript-built-in-sort-method-4f17)



### Two Pointer

```javascript
/**
 * @param {number[]} people
 * @param {number} limit
 * @return {number}
 */
var numRescueBoats = function(people, limit) {
    people.sort((a,b)=>a-b);
    let i = 0, j = people.length-1, ans = 0;
    
    while(i <= j){
        ans++;
        if(people[i] + people[j] <= limit){
            i++;
            j--;
        }
        else {
            j--;
        }
    }
    
    return ans;
    
};
```


| Time(O(N log N)) | Space(O(N)) |
| ---------------- | ----------- |
| 160 ms           | 46.3 MB     |



## 3. Epilogue 

What I've learned from this exercise:

- Be more intuitive I guess.
