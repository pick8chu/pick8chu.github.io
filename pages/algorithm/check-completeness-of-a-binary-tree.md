---
title: Check Completeness of a Binary Tree
last_updated: Mar 15, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: check-completeness-of-a-binary-tree.html
---

## #. Problem

[Go to the problem on leetcode](https://leetcode.com/problems/check-completeness-of-a-binary-tree/)


## 1. My approach

Using a queue, doing BFS. But I couldn't go further from there. When its left or right child is empty, I won't put that on the queue, which makes it impossible to know, unless I'm counting levels of tree as going through the queue. However, that felt a bit too excessive. <br/> 


## 2. Solution

It was very close to my approach, I can just add empty node as null to the queue, and when the queue pops out a null, I can just check if the rest of the queue is empty or not. <br/> 

```typescript
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

function isCompleteTree(root: TreeNode | null): boolean {
    const q = [root];

    while(q.length){
        const curRoot = q.shift();

        if(!curRoot) return q.every(el => !el);

        q.push(curRoot.left? curRoot.left:null);
        q.push(curRoot.right? curRoot.right:null);
    }

    return false;
};
``` 

## 3. Epilogue 

Very simple problem that requires some creativity. <br/>

What I've learned from this exercise:

- I should've thought of using null as a placeholder for empty node.