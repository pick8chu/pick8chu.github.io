---
title: leetcode- Jump Game VI
last_updated: Jun 10th, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: jump-game-vi.html
summary: Brilliant way of using dequeue

---

## #. Problem

[Link](https://leetcode.com/problems/jump-game-vi/)



## 1. My approach

Brute-force. Quite straight forward DP, I thought this would work.


```c++
class Solution {
public:
    int maxResult(vector<int>& nums, int k) {
        int N = nums.size();
        for(int i = 1; i < N; i++){
            int cur_max = -1000000001;
            for(int j = i-k < 0? 0:i-k; j <= i-1; j++){
                cur_max = max(cur_max, nums[j]);
            }
            nums[i] = cur_max + nums[i];
        }
        
        return nums.back();
    }
};
```



This gave me time limit. 

| Time(O(n*k)) | Space(n) |
| ------------ | -------- |
| ? ms         | ? MB     |



## 2. [Solutions](https://leetcode.com/problems/jump-game-vi/discuss/1260737/Optimizations-from-Brute-Force-to-Dynamic-Programming-w-Explanation)

Using Dequeue. What I was really surprised was, that this guy stored indexes not actual values. By doing so, it's quite impressive how he made it work.



**ALL CREDITS TO "archit91", https://leetcode.com/problems/jump-game-vi/discuss/1260737/Optimizations-from-Brute-Force-to-Dynamic-Programming-w-Explanation**



```c++
class Solution {
public:
    int maxResult(vector<int>& nums, int k) {
        int N = nums.size();
        vector<int> dp(N);
        dp[0] = nums[0];
        deque<int> q;
        q.push_back(0);
        for(int i = 1; i < N; i++) {
            //	if q.front is outside of range of k, pop_front().
            //	maximum violation of range would be 1, therefore, 
            //	you don't use while. precise
            if(q.front() < i - k) q.pop_front();
            
			dp[i] = nums[i] + dp[q.front()];
            
            //	I am not sure why deleting from back.
            //	but if I deleted from front it won't work.
            while(!q.empty() && dp[q.back()] <= dp[i]){
                q.pop_back();
            }
            
            //	when the q was empty from avobe step,
            //	this will leave one element in the q
            q.push_back(i);
        }
        return dp.back();
    }
};
```

waiting for the reply : [here](https://leetcode.com/problems/jump-game-vi/discuss/1260737/Optimizations-from-Brute-Force-to-Dynamic-Programming-w-Explanation/968447)



| Time(O(n)) | Space(n) |
| ---------- | -------- |
| 312 ms     | 80.5 MB  |



## 3. Epilogue 

What I've learned from this exercise:

- You just have to be smart 😕
