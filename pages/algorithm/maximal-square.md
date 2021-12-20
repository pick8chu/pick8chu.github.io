---
title: leetcode- Maximal Square
last_updated: Dec 20, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: maximal-square.html

---

## #. Problem

[Maximal Square](https://leetcode.com/problems/maximal-square/)



## 1. My approach

### brute force

very inefficient.

## 2. Solution

### Dynamic Programming

Link is [here](https://leetcode.com/problems/maximal-square/solution/)

Quite clever.
Basically, if(map(i,j) == 1) then, dp(i,j)=min(dp(i−1,j),dp(i−1,j−1),dp(i,j−1))+1

So, you can go from left top to right bottom, O(M\*N).
It's easy to understand if you draw the matrix and try it by yourself.

{% include image.html file="algorithm/maximal-square-img.png" %}
