---
title: leetcode- Power of Two
last_updated: Dec 21, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: power-of-two.html

---

## #. Problem

[Power of Two](https://leetcode.com/problems/power-of-two/)



## 1. My approach

### brute force

Even if I divided it by 2, the whole process will only take 32 times even on the worst case.

```
bool isPowerOfTwo(int n) {
    if(n <= 0) return false;

    while(n%2 == 0){
        n /= 2;
    }

    if(n == 1) return true;
    else return false;
}
```


## 2. Solution

### cleaver

Very easy and efficient. 

Based on
1. Binary code of power of 2 would only have one 1. ex) 000100
2. power of 2 can't be smaller than 0

You can come up with the solution below.

```
bool isPowerOfTwo(int n) {
   if(n<=0) return false;
   return (n & (n-1))? false:true;
}
```

0 0 0 1 0 0 0 0 0 <br/>
0 0 0 0 1 1 1 1 1 <br/>
& <br/>
0 0 0 0 0 0 0 0 0
  
n & (n - 1) cannot be true when n is power of 2.
