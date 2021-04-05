---
title: leetcode- Palindrome Linked List
last_updated: April 05, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: palindrome-linked-list.html
summary: map

---

## #. Problem

[Problem link](https://leetcode.com/problems/palindrome-linked-list/)



## 1. My approach 

Since it's one way linked list, i had to store all the data to compare left, and right directions at the same time. So, I used String to store them (since it was 1 digit, if it were more than one I could've used separators), and check if it is Palindrome.



```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if(head == NULL) return false;
        
        string temp = "";
        
        while(head != NULL){
            temp += to_string(head->val);
            head = head->next;
        }
        
        for(int i = 0, j = temp.size()-1; i <= j; i++, j--){
            if(temp[i] != temp[j]){
                return false;
            }
        }
        
        return true;
    }
};
```

| Time - O(N) | Space - O(1) |
| ----------- | ------------ |
| 332 ms      | 120.4 MB     |



But it turned out that it takes way more time to append string, it was way faster in Vector.  and also very efficient in space.



```c++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> v;
        while(head != NULL){
            v.push_back(head->val);
            head = head->next;
        }
        
        for(int i = 0, j = v.size()-1; i <= j; i++,j--){
            if(v[i] != v[j]) return false;
        }
        
        return true;
    }
};
```

| Time - O(N) | Space - O(N) |
| ----------- | ------------ |
| 28 ms       | 15.1 MB      |



## 2. Different solution

### Without making any other structure

```c++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if(!head || !head -> next){
            return true;
        }
        ListNode* slow = head, *fast = head;
        // when fast goes to the end, slow will be in the middle(1/2)
        while(fast->next && fast->next->next){
            slow = slow -> next;
            fast = fast -> next -> next;
        }
        // making a reverse linked list from slow to 1
        slow -> next = reverseList(slow -> next);
        slow = slow -> next;
        // comparison
        while(slow){
            if(head -> val != slow -> val){
                return false;
            }
            head = head -> next;
            slow = slow -> next;
        }
        return true;
    }
    
    ListNode* reverseList(ListNode* head){
        ListNode *prev  =nullptr, *next;
        while(head){
            next = head -> next;
            head -> next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
};
```



| Time = O(N) | Space - O(1) |
| ----------- | ------------ |
| 16 ms       | 9.7 MB       |



## 3. Epilogue 

What I've learned from this exercise:

- Be creative... I guess concentrate more?
