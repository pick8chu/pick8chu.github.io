---
title: Merge K Sorted Lists
last_updated: Mar 07, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: merge-k-sorted-lists.html
---

## #. Problem

[Go to the problem on leetcode](https://leetcode.com/problems/merge-k-sorted-lists/)


## 1. My approach

Merging to the one *TreeNode* as going through the loops. <br/> It would take O(nk) time complexity, where *n* is the number of nodes and *k* is the number of lists. <br/>

```typescript
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function mergeKLists(lists: Array<ListNode | null>): ListNode | null {
    if(!lists.length) return null;

    let main = lists[0];
    for(let i = 1; i < lists.length; i++){
        // console.log(`loop ${i} ::`);

        let mainHead = main;
        let subHead = lists[i];
        let temp = new ListNode(), ptr = temp;

        while(mainHead && subHead){
            if(mainHead.val > subHead.val) {
                ptr.next = subHead;
                subHead = subHead.next;                
            } else {
                ptr.next = mainHead;
                mainHead = mainHead.next;
            }
            ptr = ptr.next;
        } 

        // for(let tempPtr = temp; tempPtr; tempPtr = tempPtr.next) console.log(`  ${tempPtr.val} ->`)

        if(mainHead) ptr.next = mainHead;
        if(subHead) ptr.next = subHead;

        main = temp.next;
    }

    return main;
};
```


## 2. Better approach

Using a strategy of *divide and conquer*. <br/> It would take O(n log k) time complexity, where *n* is the number of nodes and *k* is the number of lists. <br/>

```typescript
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

function mergeKLists(lists: Array<ListNode | null>): ListNode | null {
    if(!lists.length) return null;

    let newList = [...lists];
    while(newList.length > 1){
        const tempList = [];
        for(let i = 0; i < newList.length; i += 2){
            // console.log(`loop ${i} ::`);

            let mainHead = newList[i];
            let subHead = newList[i+1];
            let temp = new ListNode(), ptr = temp;

            while(mainHead && subHead){
                if(mainHead.val > subHead.val) {
                    ptr.next = subHead;
                    subHead = subHead.next;                
                } else {
                    ptr.next = mainHead;
                    mainHead = mainHead.next;
                }
                ptr = ptr.next;
            } 

            // for(let tempPtr = temp; tempPtr; tempPtr = tempPtr.next) console.log(`  ${tempPtr.val} ->`)

            if(mainHead) ptr.next = mainHead;
            if(subHead) ptr.next = subHead;

            tempList.push(temp.next);
        }
        newList = tempList;
    }

    return newList[0];
};
``` 

## 3. Epilogue 

I didn't expect them to have huge difference in terms of time complexity, because typescript is not a language that is optimized for speed. <br/> But it turns out that the second approach is much faster than the first one. <br/>

**The differences were more than 3 times.** <br/>


| 1. my approach   | 2. better approach |
| ---------------- | ------------------ |
| 291 ms           | 91 ms              |
| 47.8 MB          | 48.6 MB            |

What I've learned from this exercise:

- Time complexity approach is still very important even for the language that is not optimized for speed.