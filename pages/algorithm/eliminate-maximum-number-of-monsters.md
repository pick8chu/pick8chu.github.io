---
title: House Robber III
last_updated: Nov 7, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: house-robber-iii.html
---

## #. Problem

### <a href="https://leetcode.com/problems/house-robber-iii/" target="_blank">Go to the problem on leetcode</a>


## 1. My approach (DFS)

#### First attempt: 
Comparing with `ans` at the end of the node.
```typescript
...
  function dfs(root: number, curAmt: number, prevUsed: boolean){
    if(!root) {
      ans = Math.max(ans, curAmt);
      return;
    }
...
```
Problem: It will only calculate and compare one traversal(left one, or right one)

#### Second attempt:
It has to return value to calculate both traversal(left + right)
```typescript
...
  function dfs(root: number){
    if(!root.left && !root.right) {
      return root.val;
    }
...
```
Problem: This won't be able to implement the concept of skipping one for the other one.

#### Third attempt:
Since it's bruteforce anyways, let's give it every possible scenario.
```typescript
function rob(root: TreeNode | null): number {
    function bruteForce(root: TreeNode): [number, number]{
        if(!root.left && !root.right) return [root.val, 0];

        const [leftUsed, leftNotUsed] = root.left? bruteForce(root.left): [0, 0];
        const [rightUsed, rightNotUsed] = root.right? bruteForce(root.right) : [0, 0];

        const usingMe = leftNotUsed + rightNotUsed + root.val;
        const notUsingMe = Math.max(leftNotUsed + rightNotUsed, leftUsed + rightUsed, leftUsed + rightNotUsed, leftNotUsed + rightUsed);
        // Same as: notUsingMe = Math.max(leftUsed, leftNotUsed) + Math.max(rightUsed, rightNotUsed);
        return [usingMe, notUsingMe];
    };

    return Math.max(...bruteForce(root));
};
```
Worked.

_O(N)_


## 3. Epilogue 

What I've learned from this exercise:

- I need to soften my brain...
