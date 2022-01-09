---
title: leetcode- Complement of Base 10 Integer
last_updated: Jan 9, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: complement-of-base-10-integer.html
summary: very clever bitwise operation

---

## #. Problem

[Link](https://leetcode.com/problems/complement-of-base-10-integer/)



## 1. Solution

Very smart. If you get negative number-1 for *n*, you would get all 1's of left hands.

```c++
// e.g.)
// 5 : 00...00101
// -6 : 11...11010
```

Therefore, put all right sides 1s till it's less than *n*, and do XOR.

XOR. If two digits are the same : 0, if it's different : 1

```c++
class Solution {
public:
    int bitwiseComplement(int n) {
        int mask = 1;
        while(mask < n){
            mask = (mask << 1) + 1;
        }
        
        return mask^n;
    }
};
```



## 2. Epilogue 

What I've learned from this exercise:

- adding several operation together could be a good way to start solving a problem 

