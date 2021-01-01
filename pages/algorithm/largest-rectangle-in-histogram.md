---
title: leetcode- Largest Rectangle In Histogram
last_updated: Jan 01, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: largest-rectangle-in-histogram.html

---

## #. Problem

[Largest Rectangle In Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)



## 1. My approach

### 3 for loops

Even though it has 3 for loops, due to 'i = j', it's time complexity should be O(N^2).

```javascript
/**
 * @param {number[]} heights
 * @return {number}
 */
var largestRectangleArea = function(heights) {
    let h = 1;
    // let max = heights.reduce((r, item)=>Math.max(r, item), 0);
    let m = new Set()
    // let m = [];
    heights.forEach(item => m.add(item));
    let curAns = 0;
    
    for(let me of m){
        for(let i = 0; i < heights.length; i++){
            if(heights[i] >= me){
                let j = i+1;
                while(j < heights.length && heights[j] >= me) j++;
                // console.log(`${h}일때, ${i}부터 ${j}까지`)
                curAns = Math.max(curAns, (j-i)*me);
                i = j;
            }
        }
        
        h++;
    }
    
    return curAns;
};
```



## 2. Mono Stack

> monostack: stack which has the following invariant: elements inside will be always in increasing order. 

Detail Explanations are below:

1. [Discussion page](https://leetcode.com/problems/largest-rectangle-in-histogram/discuss/995249/Python-increasing-stack-explained

It only takes O(N) time. 



### Implement

We will store indexes in a stack. Since it's a mono-stack, when the top element of the stack is we have to pop it, and we will calculate possible rectangles, but getting width is tricky. Hopefully that comment would help.

```javascript
/**
 * @param {number[]} heights
 * @return {number}
 */
var largestRectangleArea = function(heights) {
    let stack = [];
    
    let curAns = 0;
    heights.push(0);
    stack.push(0);
    
    for(let i = 1; i < heights.length; i++){
        while(heights[i] < heights[stack[stack.length-1]]){
            let idx = stack.pop();
            // console.log(`${heights[idx]} -> ${i},${idx}`)
            
            // i - stack[stack.length-1] - 1 : if there's still elements in stack,
            // it would means that it was small enough to not get popped which means,
            // it can't be bigger than 'curHeight'.
            // i : is when stack is empty.
            
            // good example case for this would be, [2,1,6,5,2,3] => ans: 10
            let curHeight = heights[idx];
            let curWidth = stack.length? i - stack[stack.length-1] - 1 : i;
            curAns = Math.max(curHeight * curWidth, curAns);
        }
        stack.push(i);
    }
    
    return curAns;
};
```



|                      | Time   | Space   |
| -------------------- | ------ | ------- |
| My approach - O(N^2) | 520 ms | 41.1 MB |
| Mono Stack - O(N)    | 76 ms  | 42.1 MB |


## 3. Epilogue 

What I've learned from this exercise:

- Well. I should know how to use stack more creatively by now..
