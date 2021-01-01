---
title: leetcode- Pseudo-Palindromic Paths in a Binary Tree
last_updated: Jan 01, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: pseudo-palindromic-paths-in-a-binary-tree.html

---

## #. Problem

[Pseudo-Palindromic Paths in a Binary Tree](https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/)



## 1. My approach

### Map and Set

At first I actually uses set to avoid the redundancy but it won't be accepted, so I just changed to a list. Time complexity would be log N since it's going from root to every leaves, and result string would be also log N(root -> leaf), and map.size < log N.

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var pseudoPalindromicPaths  = function(root) {
    
    // let set = new Set();
    let set = [];
    
    var dfs = (root, s) => {
        s = s + (root.val).toString();
        
        if(!root.left && !root.right) {
            let m = new Map();
            for(let i of s){
                m.set(i, (m.get(i)? m.get(i):0) + 1);
            }
            let flagOdd = false;
            for(let i of m){
                if(i[1] % 2){
                    if(flagOdd) return;
                    else flagOdd=true;
                }
            }
            // console.log(s.split('').sort().join(''));
            set.push(s);
            return;
        }
        
        if(root.left) dfs(root.left, s);
        if(root.right) dfs(root.right, s);
    }
    
    dfs(root, '');
    // console.log(set);
    return set.length;
};
```



## 2. Solution(Bit masking)

> bit masking : mask information using bit codes

Detail Explanations are below:

1. [Leetcode Solution Page](https://leetcode.com/problems/pseudo-palindromic-paths-in-a-binary-tree/solution/)



### Implement

S would start as '... 0000 0000 0000', and when there's 4 then it would mask as '... 0000 1000'. Also as doing XOR operation, you will get rid of the numbers that occurs even times. However, when there was more than two odd occurring numbers, it would be looking something like '0010 0010' when there were two odd occurring numbers. 

's & (s - 1)' this would remove the first digit, so '0010 0010' would become '0000 0010'. If the result is not 0, it means there were more than two odd occurring numbers.

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number}
 */
var pseudoPalindromicPaths  = function(root) {
    
    let set = new Set();
    let ans = 0;
    var dfs = (root, s) => {
        if(root){
            s = s ^ (1 << root.val)

            if(!root.left && !root.right) {
                
                if((s & (s - 1)) == 0){
                    ans++;
                }
                return;
            }

            dfs(root.left, s);
            dfs(root.right, s);
        }
    }
    
    dfs(root, 0);
    return ans;
};
```



|                        | Time   | Space   |
| ---------------------- | ------ | ------- |
| My approach - O(log N) | 352 ms | 75.1 MB |
| Solution - O(log N)    | 248 ms | 77.3 MB |


## 3. Epilogue 

What I've learned from this exercise:

- Bit masking, cool stuff!
