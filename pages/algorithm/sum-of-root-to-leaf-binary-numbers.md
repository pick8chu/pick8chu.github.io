---
title: leetcode- Sum of Root To Leaf Binary Numbers
last_updated: Jan 11, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: sum-of-root-to-leaf-binary-numbers.html
summary: cleaver way to convert binary number to decimal number 
---



## #. Problem

[Link](https://leetcode.com/problems/sum-of-root-to-leaf-binary-numbers/)



## 1. My approach

So, the problem itself is not very difficult, but the part that it might be tricky is to convert binary number to decimal number. Since the length of the binary number could be 1000 long, I decided to use String and convert them after it reached to the leaf nodes.

my code is below;
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private:
    int ans;
    
public:
    int sumRootToLeaf(TreeNode* root) {
        ans = 0;
        
        dfs(root, "");
        
        return ans;
    }
    
    void dfs(TreeNode* curNode, string curS){
        if(not curNode) return;
        curS += to_string(curNode->val);

        if(not curNode->right && not curNode->left) {
            ans += binaryToInt(curS);
        }
        else {
            dfs(curNode->right, curS);
            dfs(curNode->left, curS);
        }
    }
    
    int binaryToInt(string input){
        int tempAns = 0;
        int n = input.size()-1;
        for(int i = 0; i < input.size(); i++){
            tempAns += (input[i] - '0')*pow(2, n-i);
        }
        // cout<< input << " : " << tempAns << endl;
        
        return tempAns;
    };
    
};
```

Although the time complexity and space complexity won't make a huge difference, here are those:

| Time(O(Number of Nodes * depth)) | Space(O(tree height)) |
| ------------ | ----------- |
| 4 ms       | 18.5 MB     |





## 2. Solutions

It's pretty much the same except how it calculate the binary number to decimal number.
It multiply 2 to its current sum everytime it goes deeper, because whenever it does it will have new number on its right side. Very cleaver, and smart.

```c++
class Solution {
public:
    int sumRootToLeaf(TreeNode* root) {
        return dfs(root,0);
    }
 public :
    int dfs(TreeNode* root ,int val)
    {
        if(root == NULL)
            return 0;
        val = 2*val + root->val;
        if(root->left == NULL && root->right == NULL)
            return val;
        else
        {
            int left = dfs(root->left,val);
            int right = dfs(root->right,val);
            return left + right;
        }
        
    }
};
```


| Time(O(number of nodes)) | Space(O(tree height)) |
| ------------ | ------------- |
| 0 ms         | 16.8 MB          |



## 3. Epilogue 

What I've learned from this exercise:

- Very smart and well applicated.
