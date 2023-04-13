---
title: Check if Object Instance of Class
last_updated: Apr 13, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: check-if-object-instance-of-class.html
---

## #. Problem

This problem is not an algorithm related, it's more towards to understanding of the javascript. <br/>
The purpose of this is to remember this problem and solution. <br/>

[Go to the problem on leetcode](https://leetcode.com/problems/check-if-object-instance-of-class/)


## 1. My approach

Something obvious.

```typescript
function checkIfInstanceOf(obj: any, classFunction: any): boolean {
  // or obj instanceof classFunction
    return typeof obj === typeof classFunction;
};
```


## 2. Solution

Every JS object has prototype, and every prototype has constructor. <br/>
So, we can track its parent to check if the object is an instance of the class. <br/>

```typescript
function checkIfInstanceOf(obj: any, classFunction: any): boolean {
    if (obj === null || obj === undefined || !classFunction) {
        return false;
    }

    let ctor: any = obj.constructor;

    while (ctor) {
        if (ctor === classFunction) {
            return true;
        }
        ctor = Object.getPrototypeOf(ctor.prototype)?.constructor;
    }

    return false;
};
``` 

## 3. Epilogue 

Honestly, in production, this kind of problem is not something common. <br/>
However, it is still valid to have a good understanding of programming languages. <br/>

What I've learned from this exercise:

- importancy of understanding the language