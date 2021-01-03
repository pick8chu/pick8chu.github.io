---
title: HackerRank- Array Manipulation
last_updated: Jan 03, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: array_manipulation.html

---

## #. Problem

[Array Manipulation](https://www.hackerrank.com/challenges/crush/problem)



## 1. My approach

### 2 fors - O(MN)

Quite straight forward, except you don't know the limit time complexity of this problem. Obviously O(MN) is a fail since MN = 10^16 or something.

I couldn't think of the better idea tho. I thought about segment tree but it would take more time than this.

```javascript
// Complete the arrayManipulation function below.
function arrayManipulation(n, queries) {
    let arr = Array.from({length:n+1},()=>0);
    let maxll = 0;
    for(let el of queries){
        for(let i = el[0]; i <= el[1]; i++){
            arr[i] += el[2];
            maxll = Math.max(maxll, arr[i]);
        }
    }
    
    return maxll;
}
```



## 2. Brilliant 1 for - O(N)

I found this on discuss page, and it is super smart. Since it's just about getting the max value, you don't have to add to all the elements, but only to beginning and at the end you just take that off.



### Implement

```javascript
function arrayManipulation(n, queries) {
    // n+2 because of el[1]+1.
    let arr = Array.from({length:(n+2)},()=>0);
    for(let el of queries){
        arr[el[0]] += el[2];
        arr[el[1]+1] -= el[2];
    }
    
    let ans = 0;
    let tmpAns = 0;
    for(let i of arr){
        tmpAns += i;
        ans = Math.max(ans,tmpAns);
    }
    
    return ans;
}
```




## 3. Epilogue 

What I've learned from this exercise:

- Be smart...??
