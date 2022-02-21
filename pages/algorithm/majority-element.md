---

title: Majority Element
last_updated: Feb 22, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: majority-element.html


---


## #. Problem

[Link](https://leetcode.com/problems/majority-element/)



## 1. My approach

Just an easy for loop with a Map.

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int size = nums.size();
        map<int, int> m;
        
        for(auto &num : nums){
            m[num]++;
            if(m[num] > size/2) return num;
        }
        // it won't reach here
        return 0;
    }
};
```
| Time(O(N)) | Space(O(1)) |
| ---------- | ----------- |
| 18 ms      | 19.6 MB     |



## 2. [Boyer–Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)

*Boyer–Moore majority vote algorithm* is to get the candidate with the majority votes. 

> The **Boyer–Moore majority vote algorithm** is an [algorithm](https://en.wikipedia.org/wiki/Algorithm) for finding the [majority](https://en.wikipedia.org/wiki/Majority) of a sequence of elements using [linear time](https://en.wikipedia.org/wiki/Linear_time) and constant space.

```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int count = 0, n = 0;
        
        for(int i = 0; i < nums.size(); i++){
            if(count == 0) n = nums[i];
            if(nums[i] == n) count++;
            else count--;
        }
        return n;
    }
};
```

Idea is to cancel out the same number, and when the *count* = 0, get new candidate and do the same thing till the end. It's easier to understand to look at the picture.

[![](https://upload.wikimedia.org/wikipedia/commons/c/c7/Boyer-Moore_MJRTY.svg)](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)




| Time(O(N)) | Space(O(1)) |
| ---------- | ----------- |
| 25 ms      | 19.6 MB     |



## 3. Epilogue 

What I've learned from this exercise:

- Boyer–Moore majority vote algorithm
