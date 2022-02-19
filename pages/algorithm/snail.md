---
title: Snail
last_updated: Feb 19th, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: snail.html


---

## #. Problem

[Find the unknown digit](https://www.codewars.com/kata/521c2db8ddc89b9b7a0000c1/train/javascript)

{% include image.html file="image-20220219124907910.png"  %}
<!-- ![](android/google_map_01.png) -->

## 1. My approach

I tried to make 4 functions for each direction.

I gave up because it was too complex to follow.





## 2. Solutions

What you can do is, to remove each row or column after you finish them.

Basic idea is to have a frame for elements that have not been calculated.



### a. Best practice

```js
snail = function(array) {
  var result;
  while (array.length) {
    // Steal the first row.
    result = (result ? result.concat(array.shift()) : array.shift());
    // Steal the right items.
    for (var i = 0; i < array.length; i++)
      result.push(array[i].pop());
    // Steal the bottom row.
    result = result.concat((array.pop() || []).reverse());
    // Steal the left items.
    for (var i = array.length - 1; i >= 0; i--)
      result.push(array[i].shift());
  }
  return result;
}
```



### b. More general solution

```javascript
snail = function(arr) {
  let ans = [];
  let n = arr[0].length;
  if(n == 0) return ans;
  let xl = 0, xr = n-1; 
  let yt = 0, yb = n-1;
  let x = 0, y = 0;
  console.log(xl, xr, yt, yb);
  while(xl <= xr && yb >= yt){
    while(x < xr) ans.push(arr[y][x++]);
    yt++;
    while(y < yb) ans.push(arr[y++][x]); 
    xr--;     
    while(xl < x) ans.push(arr[y][x--]); 
    yb--;     
    while(yt < y) ans.push(arr[y--][x]);      
    xl++;
    console.log(arr);
  }
  ans.push(arr[y][x]);
  return ans;
}

```



## 3. Epilogue 

What I've learned from this exercise:

- Codewar is better for studying codings.

