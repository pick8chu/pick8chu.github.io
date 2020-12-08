---
title: leetcode- Kth Smallest Element in a BST
last_updated: Dec 08, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: Kth_Smallest_Element_in_a_BST.html

---

## #. Problem

[Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)



## 1. My approach

I got none.



## 2. Recursive Inorder Traversal

Applying in-order traversal was an interesting approach which works well. So I recursively made a vector of int and return the k th element.

### w/ Global variable

```c++
class Solution {
    // v as a global variable
    vector<int> v;
public:
    void inorder(TreeNode* root){
        if(not root) return;
        
        if(root->left) inorder(root->left);
        v.push_back(root->val);
        if(root->right) inorder(root->right);
    }
    
    int kthSmallest(TreeNode* root, int k) {
        inorder(root);        
        return v[k-1];
    }
};
```

### w/o Global variable

```c++
class Solution {
public:
	// use v as a reference parameter
    void inorder(TreeNode* root, vector<int>& v){
        if(not root) return;
        
        if(root->left) inorder(root->left, v);
        v.push_back(root->val);
        if(root->right) inorder(root->right, v);
    }
    
    int kthSmallest(TreeNode* root, int k) {
        vector<int> v;
        inorder(root, v);        
        return v[k-1];
    }
};
```



Interestingly enough, using reference parameter gives better performance. I guess referring a global variable is no better than passing by references. 



|                     | Time  | Space   |
| ------------------- | ----- | ------- |
| w/ global variable  | 40 ms | 24.8 MB |
| w/o global variable | 20 ms | 24.8 MB |



## 3. Iterative Inorder Traversal

It should have the same time complexity except it returns it right after it finds the target. Even with that, the worst case would take the same time. I just wanted to add this so that I could think about the algorithm, since I didn't come up with it.



```c++
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode*> s;
        
        while(true){
            while(root != NULL){
            	// go left all the way to the end 
                s.push(root);
                root = root->left;
            }
            // and check it if it's k th
            root = s.top();
            s.pop();
            if(--k == 0) return root->val;
            // and see it's right tree
            root = root->right;
        }
    }
};
```



It's better with pics ([here](https://leetcode.com/problems/kth-smallest-element-in-a-bst/solution/))

| Time  | Memory  |
| ----- | ------- |
| 20 ms | 24.6 MB |



## 4. Epilogue 

What I've learned from this exercise:

- Inorder traversal could be used to get an ordered array.
- How to get inorder traversal iteratively.
