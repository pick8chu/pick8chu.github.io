---
title: leetcode- rotate array
last_updated: Nov 5, 2020
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: rotate_array.html
summary: hmmm can be tricky, challenging if you wanna use O(1) space complexity.

---

## #. Problem

https://leetcode.com/problems/rotate-array/

Since it says that one can achieve it with O(1) of space complexity, I firstly tried to solve it without using any vectors or etc., but I couldn't get to the answer. The day after, I solved it with using another vector, and little later I also solved it with O(1) space complexity. However, actual space complexity that is shown on the website doesn't make a huge differences. so .. I came to the conclusion that most of the time, sacrificing space in order to shorten the time is good.



## 1. My approach (T: O(n), S: O(n))

My idea was, of course using brute force at first but it throw exceeded time limit error. So, I thought of this idea of making a new vector and concatenating two chunk of original vector corresponding the constant 'k'.

```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if(nums.size() < 2) return;
        k %= nums.size();
        
        vector<int> v(nums.size());
        move(nums.end()-k, nums.end(), v.begin());
        move(nums.begin(), nums.end()-k, v.begin()+k);
        nums = v;
        
        return;
    }
};
```

I'm not so certain of it's time complexity to be honest. But according to [cpp reference website](https://en.cppreference.com/w/cpp/algorithm/move), it says 'last - first move assignments'. So I'm guessing it would be O(n). 

PS. 
$$
O(nums.end() - (nums.end()-k) + nums.end()-k - nums.begin()) = O(nums.size())
$$


| Time - O(n) | Space - O(n) |
| ----------- | ------------ |
| 8 ms        | 10.5 MB      |

## 2. With O(1) space (T: O(n), S: O(1))

So the idea is, to keep one value and move elements accordingly. Here's an example:

nums = [1, 2, 3, 4, 5, 6, 7]

k = 3

loop 1:

​	temp = 7

​	nums = [1, 2, 3, 4, 5, 6, **4**]

loop 2:

​	temp = 7

​	nums = [1, 2, 3, **1**, 5, 6, 4]

loop 3:

​	temp = 7

​	nums = [**5**, 2, 3, 1, 5, 6, 4]

loop 4:

​	temp = 7

​	nums = [5, 2, 3, 1, **2**, 6, 4]

loop 5:

​	temp = 7

​	nums = [5, **6**, 3, 1, 2, 6, 4]

loop 6:

​	temp = 7

​	nums = [5, 6, 3, 1, 2, **3**, 4]

loop 7:

​	temp = 7

​	nums = [5, 6, **7(temp)**, 1, 2, 3, 4]

ans:

​	nums = [5, 6, 7, 1, 2, 3, 4]

However, it has some problems. Odd numbered length vectors work fine, but even numbered length vectors have some problems, it might be able to keep switching the elements those have already been switched. To solve this problem, whenever next changing index of vector has already changed, I subtracted 1 and did the same for the last.

explanation and pictures are in [here(the solution page)](https://leetcode.com/problems/rotate-array/solution/).

```java
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        int idx = nums.size() - 1;
        int savedIdx = idx;
        int temp = nums[idx];
        for (int i = 0; i < nums.size()-1; i++) {
            int tempIdx = idx;
            if (idx - k < 0) {
                idx = idx - k + nums.size();
                if(idx == savedIdx){
                    nums[tempIdx] = temp;
                    idx--;
                    savedIdx = idx;
                    temp = nums[idx];
                }
                else {
                    nums[tempIdx] = nums[idx];
                }
            }
            else {
                idx = idx - k;
                nums[tempIdx] = nums[idx];
            }
        }
        nums[idx] = temp;
    }
};a
```



This code might be little difficult to understand actually... Please write comments if you wanna know more, but [the solution of leetcode](https://leetcode.com/problems/rotate-array/solution/) explains it very well so you might wanna check that out too.



| Time - O(n) | Space - O(1)                              |
| ----------- | ----------------------------------------- |
| 12 ms       | 10.4 MB <- not really different from O(n) |



## 3. Epilogue 

What I've learned from this exercise:

- for space complexity, O(1) and O(n) might not be that big different.
