---
title: leetcode- single number
last_updated: Nov 4, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: single_number.html
summary: bit operations. 

---

## #. Problem

https://leetcode.com/problems/single-number/solution/



## 1. My approach

it's easy when I use javascript. Well, even if i don't, i guess i could solve in O(n^2)


```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    return nums.filter((item, idx, arr) =>arr.indexOf(item) == arr.lastIndexOf(item)).join('')
};
```

| Time - O(n^2) | Space - O(1) |
| ------------- | ------------ |
| 808 ms        | 40.4 MB      |



But to compare, I used C++ again.

```c++
#include <map>

class Solution {
public:
    int singleNumber(vector<int>& nums) {
        map<int,int> m;
        int ans = 0;
        for(int i = 0; i < nums.size(); i++){
            for(int j = 0; j < nums.size(); j++){
                if(nums[i] == nums[j] && j != i) break;
                else if(j == nums.size()-1) return nums[i];
            }
        }
        
        return ans;
    }
};
```

Understandingly enough, it exceeded the time limit.

| Time - O(n^2) | Space - O(1) |
| ------------- | ------------ |
| ? ms          | ? MB         |

## 2. with more space complexity

As to lower the time complexity to O(n), I had to use some sort of map, which took O(n) of space complexity.

```c++
#include <map>

class Solution {
public:
    int singleNumber(vector<int>& nums) {
        map<int,int> m;
        int ans = 0;
        for(int item : nums){
            m[item]++;
        }
        for(auto iter : m){
            if(iter.second == 1){
                ans = iter.first;
            }
        }
        
        return ans;
    }
};
```

Well at least this time it won't exceed the time limit. Little weird tho, 76*76 won't be too much, I don't get why the first attempt exceeded time limit. Hmm.

| Time - O(n) | Space - O(n) |
| ----------- | ------------ |
| 76 ms       | 21 MB        |



## 3. Bit operation(CRAZY)

So this is the reason why I'm writing this down, this attempt is very creative and super efficient. It only needs O(n) of time complexity and doesn't need any more space except the return int size.



[**Concept**](https://leetcode.com/problems/single-number/solution/)

- If we take XOR of zero and some bit, it will return that bit
  - *a* ⊕ 0 = *a*
- If we take XOR of two same bits, it will return 0
  - *a* ⊕ *a* = 0
- *a* ⊕ *b* ⊕ *a* = ( *a* ⊕ *a* ) ⊕ *b* = 0 ⊕ *b* = *b*

So we can XOR all bits together to find the unique number.



```c++
#include <map>

class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for(int item : nums){
            ans ^= item;
        }
        
        return ans;
    }
};
```



And this one only gives us 36 ms. 

| Time - O(n) | Memory - O(1) |
| ----------- | ------------- |
| 36 ms       | 17.3 MB       |



## 4. Epilogue 

What I've learned from this exercise:

- The more you know you would come up with better ideas.
- Bit operations can be useful. (&, \|, ^)
